# Observability Plan

Instrumentation for metrics, logs, and traces across services and agents.

## Metrics
- Prometheus with exporters on Laravel, FastAPI, and agent gateway.
- SLOs: WSS connect success >99.5%, command latency P95 <300ms, OTA success rate >98%, compliance eval latency P95 <200ms.
- Alerting rules for error rate >5%, consumer lag >1000 messages, CPU >80% sustained 5 minutes.

## Logs
- Structured JSON shipped via Fluent Bit/Vector to central store (Loki/ELK).
- PII redaction filters; sampling for verbose debug logs.
- Hash-chained audit logs forwarded within 60s; retention 90 days.

## Tracing
- OpenTelemetry tracing on REST/WSS paths and event bus handlers; sampling 10% baseline, 100% on errors.
- Trace context propagated to agent via command id and to kernel via IOCTL metadata.

## Dashboards
- Grafana dashboards: control channel health, OTA pipeline, policy evaluation, compliance trends, alert delivery.
- Runbook links embedded per alert.
