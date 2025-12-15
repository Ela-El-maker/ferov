# Agent Failure Modes

Enumerates expected failures and required behaviors. Each mode maps to recovery actions and telemetry signals.

| Failure | Detection | Agent Action | Backend Expectation |
| --- | --- | --- | --- |
| FastAPI unreachable | WSS connect fails >3 attempts | Enter degraded, queue commands, backoff, log `wss_unreachable` | Alert ops; do not mark device offline until 10 minutes |
| Clock skew | NTP drift >5s | Switch to limited command set; resync time; tag telemetry `unsynchronized` | Reject signed payloads until time fixed |
| Certificate expired | TLS handshake fails with expiry error | Request renewal via bootstrap REST; if blocked, quarantine | Block commands; notify admin |
| Revocation fetch fails | OCSP/CRL unreachable for >15m | Enter degraded; allow only remediation commands | Alert; force fetch once reachable |
| Local DB corruption | Integrity check fails | Restore from last snapshot; if none, re-enroll | Require re-attestation after restore |
| KernelService offline | IOCTL health probe fails | Attempt service restart once; fallback to user-mode implementations where safe | Flag device non_compliant |
| OTA rollback | Update fails checksum/launch | Rollback to previous snapshot; mark `rollback_performed` | FastAPI blocks further OTA until reviewed |
