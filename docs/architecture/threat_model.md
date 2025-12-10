# Threat Model (summary)

- Trust boundaries: mobile user, Laravel API, FastAPI controller, Agent, KernelService
- Identities: JWT + JWKS, Ed25519 message signatures, device certificates (simulated)
- Risks: replay (nonce + TTL), tampering (signatures + hash-chained audit), privilege escalation (IOCTL allowlist), offline desync (policy hashes)
- Mitigations: canonical JSON signing, strict envelope validation, policy checks at Laravel/FastAPI/Agent, OTA signature verification stubs
