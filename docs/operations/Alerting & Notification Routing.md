# Alerting and Notification Routing

Routing rules for telemetry alerts, compliance incidents, and security events.

## Severity Model
- `critical`: security breach, revoked cert, failed driver load, repeated signature failures.
- `high`: quarantine entry, OTA rollback, compliance non_compliant persisted >10 minutes.
- `medium`: repeated rate-limit violations, degraded mode >15 minutes.
- `low`: informational state changes, policy updates.

## Routing
- Email + SMS for critical; push notification to mobile app for high/critical.
- Webhook to incident management (PagerDuty/ops channel) for critical/high.
- Correlation: combine CPU+memory spikes within 5 minutes and raise single high alert.
- Escalation: if alert unacknowledged after 10 minutes (critical) or 30 minutes (high), escalate to secondary on-call.

## Configuration
- Routing rules stored in Laravel admin portal; versioned with audit logs and preview/diff before publish.
- FastAPI applies routing rules at gateway; deduplicates alerts with same `fingerprint` within 5 minutes.
- Quiet hours supported with override for critical.

## Observability
- Metrics: `alerts.emitted`, `alerts.delivered`, `alerts.dropped`, `alerts.ack_time`.
- Delivery failures retried with exponential backoff; dead-letter queue for repeated failures.
