# WindowsAgent Update Handling

Defines the OTA install flow on Windows endpoints.

## Prerequisites
- Manifest signature verified against OTA publisher key; SBOM + provenance hashes checked before download.
- Disk space check (min 500 MB free) and battery/AC status; pause if on battery unless forced.
- Ensure KernelService health; block update if kernel in degraded mode.

## Flow
1) Download package via HTTPS with checksum verification per chunk; enforce TLS pinning.
2) Stage to `%PROGRAMDATA%/SecureAgent/ota/staging`; store snapshot of current binaries + config.
3) Verify package signature and binary digests; abort on any mismatch.
4) Drain command queue; notify FastAPI of entering update mode; switch to limited command allowlist.
5) Apply update; update service configs; keep previous version as rollback candidate.
6) Reboot coordination: if reboot required, schedule with user-facing prompt and backend notification.
7) Post-update validation: run self-tests, confirm WSS connect, attestation, and policy fetch.
8) Report success/failure with `update_id`, `version_from`, `version_to`, and elapsed time.

## Rollback
- Triggered on failed self-tests, failed WSS reconnect within 2 minutes, or repeated crashes (3 within 10 minutes).
- Restore snapshot, restart services, and mark `rollback_performed=true` for compliance checks.

## Safety
- Max two concurrent retries per update id; further attempts require backend re-issue.
- If update blocked for >7 days, raise compliance warning to backend and mobile app.
