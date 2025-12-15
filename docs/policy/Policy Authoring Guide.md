# Policy Authoring Guide

Guidelines for admins to create, test, and publish policies.

## Lifecycle
- Draft in the admin portal with schema validation against `policy_bundle_v1`.
- Run `POST /policy/validate_bundle` before publish; capture errors.
- Versioning: semantic version (major change on new rules or behavior change); keep changelog.
- Promotion: dev → staging → prod with diff + preview of affected commands/devices.

## Authoring Rules
- Reference command registry entries; include risk level, min role, quarantine allowlist flag.
- Include policy hash and timestamp; sign bundle with policy signing key.
- Define escalation behaviors (`require_2fa`, `deny`, `allow`) per command + device posture.
- Specify offline allowances and TTL for cached policy (default 10 minutes).

## Testing
- Use policy simulator with saved scenarios (device state, user role, risk score).
- Validate against recorded telemetry to catch regressions.
- Run integration tests that assert FastAPI + agent responses for high-risk commands.

## Publishing
- Require two-person review for policy changes; capture reviewer ids.
- On publish, emit event `policy.updated` with version, hash, diff summary.
- Agent must refresh policy when hash mismatch detected; deny high-risk commands until refreshed.
