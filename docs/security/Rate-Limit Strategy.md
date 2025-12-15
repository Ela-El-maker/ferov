# Rate-Limit and Abuse Prevention

Unified plan across ingress points.

## Controls
- API gateway: token bucket per IP, per user, and per device id; defaults 300 rpm/user, 120 rpm/device, 60 rpm/IP with adaptive backoff.
- WebSocket: limit concurrent sessions per device (1) and per user (5); heartbeat watchdog disconnects idle sessions after 60s.
- Command ingestion: per-command quotas (e.g., power actions max 5/hour/device) with policy overrides.
- OTA: throttle manifest checks to 1/hour/device; CDN handles byte-range throttling.
- Event bus: consumer lag alarms; producer rate caps with retries and dead-letter topics.

## Detection and Response
- Anomaly detection on request rate deltas >3x baseline; auto-quarantine offending device/user for review.
- DOS protection via WAF + CDN; fail closed for non-whitelisted methods during spikes.
- Circuit breaker trips when backend error rate >5% for 2 minutes; shed non-critical traffic.

## Instrumentation
- Emit rate-limit decision metrics (`rl.allowed`, `rl.blocked`, `rl.quota_remaining`) with labels (user/device/ip/route).
- Structured logs include `limit_id`, `bucket_key`, `retry_after`; use to tune policies.
