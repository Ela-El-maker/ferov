# Threat Model

Holistic STRIDE assessment for the Secure Device Control System. This supplements protocol JSON specs with explicit assumptions, attack surfaces, and mitigations.

## Assets and Trust Zones
- Identities: user JWT, device certificates, JWKS for services, agent-key pairs, kernel driver signing keys.
- Code + updates: agent binaries, driver INF/cab, OTA manifests, policy bundles, SBOM + provenance.
- Telemetry + commands: WSS control channel payloads, IOCTL calls, policy evaluations, audit logs.
- Infrastructure: MySQL/Redis/Kafka clusters, CA/OCSP, CI/CD artifacts, signing services.

## Attack Surfaces
- Mobile↔Laravel REST (auth, policy queries, command initiation).
- Laravel↔FastAPI REST/webhook control plane.
- FastAPI↔Agent WSS control channel, including OTA payload delivery.
- Agent↔KernelService IOCTLs and shared memory.
- CI/CD + signing pipeline for agent/driver and OTA manifests.
- Event bus producers/consumers (Kafka/Redis Streams).

## STRIDE Summary
- Spoofing: stolen JWT/device cert, forged OTA manifest, fake kernel driver. Mitigate via mTLS, Ed25519 signatures on canonical JSON, driver/agent code signing (EV/WHQL), hardware-backed key storage where available.
- Tampering: payload mutation in transit, audit log modification, OTA binary corruption. Mitigate via TLS, message signatures with sequence numbers, hash-chained local logs, signed manifests and SBOMs, integrity checks before execution.
- Repudiation: command or policy changes without trace. Mitigate via append-only audit chain, request ids, user+device binding in logs, signed policy versions, and immutably storing compliance findings.
- Information Disclosure: leakage of telemetry or secrets. Mitigate via TLS everywhere, least-privilege JWT scopes, encrypt local secrets with DPAPI, redact PII in logs, scoped policy responses.
- Denial of Service: connection floods, OTA abuse, policy cache poisoning, event bus overload. Mitigate via global rate limits, per-device backoff, circuit breakers on OTA, quota on offline queue depth, and topic retention controls.
- Elevation of Privilege: agent compromise to kernel, bypass quarantine, unsigned driver load. Mitigate via IOCTL allowlist, kernel attestation checks, quarantine deny list enforced in agent + FastAPI, and driver signature validation before install.

## Specific Scenarios
- Replay attacks: all signed payloads include monotonic sequence + nonce + `expires_at`; FastAPI rejects reused tuples; agent expires cached commands on reconnect.
- Time skew: enforce max ±5s drift; if exceeded, agent enters degraded mode, syncs via NTP, and limits commands to low-risk while telemetry is tagged `unsynchronized`.
- Update supply chain: manifest signing key stored in HSM; OTA publisher produces SBOM + provenance; agent requires matching digest + signature before staging.
- Quarantine: commands denied except allowlist (time sync, revoke, remediation). FastAPI enforces at gateway; agent mirrors deny list locally.
- Revocation: OCSP stapling preferred; fallback CRL cached with TTL; agent fetches updates within 5 minutes of notification; revoked devices move to quarantine.

## Residual Risks and Follow-ups
- Kernel exploitation mitigated but not eliminated; continuous fuzzing and driver hardening required.
- Insider misuse mitigated by audit chain but needs periodic review and just-in-time admin access.
- Dependence on external NTP/OCSP introduces availability risk; document offline grace windows and cache policies.
