# Quarantine Behavior

Defines how quarantine is enforced across UI, FastAPI, agent, and kernel.

## Entry Conditions
- Policy-triggered (compliance failure, revoked cert, high risk score).
- Manual admin action from Policy/Admin portal.
- Automatic from detection (e.g., correlated CPU+memory spike, repeated signature failures).

## Backend Rules
- FastAPI blocks all commands except `time_sync`, `revoke`, `fetch_revocations`, `reauth`, and `collect_diagnostics`.
- Laravel marks device lifecycle `quarantined`, emits event `security.quarantine.entered`, and notifies mobile app.
- Policy engine returns `deny` for high-risk commands and `require_2fa` disabled.

## Agent Behavior
- Display clear user notification; record local log entry with reason and timestamp.
- Enforce deny list locally; ignore cached commands outside allowlist.
- Attempt remediation: re-attest, fetch new policy, sync time, download latest revocation lists.
- If remediation succeeds and backend lifts quarantine, clear local state and re-run compliance check.

## Kernel Behavior
- Reject privileged IOCTLs while in quarantine except integrity self-check and rollback.
- If kernel detects tampering, it can request quarantine via signal to agent.

## Exit Conditions
- Compliance returns `compliant` after remediation; or admin manually lifts state.
- All quarantine events are audited with actor, reason, and duration; persisted 30d.
