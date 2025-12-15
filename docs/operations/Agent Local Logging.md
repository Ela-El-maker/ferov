# Agent Local Logging

Local logs must be tamper-evident, privacy-aware, and survivable across reboots.

## Format and Storage
- Structured JSON lines with fields: `ts`, `level`, `component`, `device_id`, `session_id`, `event`, `context`, `hash_prev`.
- Store under `%PROGRAMDATA%/SecureAgent/logs/`; protect folder ACL to SYSTEM+Administrators; encrypt secrets with DPAPI.
- Include rolling hash chain (`hash_prev`) to detect tampering; rotate chain daily.

## Rotation and Retention
- Rotate at 10 MB or daily (whichever first); keep 7 compressed archives locally.
- Upload to backend on reconnect; purge local copies after confirmed upload.
- If disk pressure >80%, switch to minimal log mode and emit alert.

## Redaction and Safety
- No PII in logs; token and key material must be redacted or hashed.
- Include signature verification results and policy hash mismatches with truncated payload references only.
- On tamper detection (hash break), raise quarantine request and mark archives read-only.
