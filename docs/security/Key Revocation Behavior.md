# Key Revocation Behavior

Defines how revocation signals propagate to services, agents, and kernel drivers.

## Revocation Sources
- User/device cert revoked in CA via CRL and OCSP.
- JWT signing key revoked; JWKS rotates with `not_before` timestamp.
- Agent/driver signing key compromised; OTA publisher blocks further releases and issues revocation notice.

## Propagation Rules
- FastAPI polls/staples OCSP every 5 minutes; caches CRL for 15 minutes max.
- Laravel pushes revocation events to `security.revocations.v1` topic with `entity_id`, `reason`, `effective_at`.
- Agents subscribe via control channel; must fetch revocation list within 5 minutes of receipt or enter quarantine.

## Verification on Agent
- Validate OCSP stapled responses first; fall back to CRL if OCSP unreachable.
- If neither reachable for >15 minutes, enter `degraded` state: allow only remediation commands (time sync, re-attest, fetch revocations).
- On revoked device cert: terminate sessions, purge tokens, refuse commands, and request re-enrollment.
- On JWKS rotation: fetch new keys before accepting commands; reject signatures with `kid` older than `not_before`.

## Recovery Path
- Agent retries revocation fetch with exponential backoff capped at 10 minutes.
- Once connectivity restored, re-attest and resync policy before leaving degraded state.
- Kernel driver failures to validate certs trigger rollback to previous signed driver and raise high-severity alert.
