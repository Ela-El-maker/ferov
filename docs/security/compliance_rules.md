# Compliance Rules

Authoritative checklist used by Laravel, FastAPI, and the agent to determine device posture. All checks must be deterministic, auditable, and referenceable by `compliance_version`.

## Inputs
- Agent manifest: agent version, kernel driver version, OS build, secure boot flag, TPM/attestation result, policy hash, last policy version, last update result, telemetry freshness.
- Backend state: user role, device lifecycle state, policy bundle version, revocation list version, risk score.
- Time: server-issued `evaluated_at` ISO8601 with max 5s clock skew tolerance.

## Decision States
- `compliant`: All mandatory checks pass and device is not quarantined.
- `non_compliant`: One or more mandatory checks fail; include `failed_rules` list.
- `unknown`: Missing required signals (e.g., telemetry older than 5m) or signature mismatch; force re-attestation and block high-risk commands.

## Mandatory Checks
- Version gates: agent ≥ min version, kernel driver ≥ min version for platform.
- Policy alignment: agent `policy_hash` matches server active bundle; mismatch triggers forced bundle fetch.
- Update status: last OTA result not failed; rollback flag cleared.
- Attestation: latest attestation within TTL (default 15m) and not revoked; OCSP/CRL reachable or stapled proof present.
- Risk: risk score below quarantine threshold.

## Enforcement
- Laravel ComplianceController returns state + `failed_rules` + `remediation` tips.
- FastAPI gateway blocks command dispatch on `non_compliant`; allows only low-risk `unknown` commands with `require_2fa`.
- Agent displays remediation guidance and attempts automatic recovery (update, re-attestation) before surfacing to user.

## Scheduling
- On connect, on policy change, post-OTA, and every 10 minutes via FastAPI scheduled evaluator.
- Persistent findings emitted to event bus topic `compliance.findings.v1` with schema versioning and retention 30d.
