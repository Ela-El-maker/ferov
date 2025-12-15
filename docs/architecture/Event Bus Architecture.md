# Event Bus Architecture

Defines Kafka/Redis Streams usage for control-plane events.

## Topics
- `policy.updated.v1`: policy hash, version, diff summary.
- `compliance.findings.v1`: device id, state, failed rules, remediation, evaluated_at.
- `security.revocations.v1`: entity id, type, reason, effective_at.
- `ota.release.published.v1`: release id, version, rollout stage, manifest hash.
- `alerts.emitted.v1`: alert id, severity, source, fingerprint.

## Conventions
- Naming: `<domain>.<event>.<version>`; payloads are canonical JSON with schema registry id.
- Partitions: hash by `device_id` or `tenant_id` to preserve ordering.
- Retention: defaults 30 days; compacted for reference topics (policy, revocations).
- Consumer groups: per service (`fastapi`, `laravel`, `analytics`, `mobile-push`) with idempotent handling.

## Reliability
- At-least-once delivery; idempotent event handlers with `event_id`.
- Dead-letter topics per domain; monitored for growth.
- TLS + mTLS between producers/consumers; ACLs per topic.
