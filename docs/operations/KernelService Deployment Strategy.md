# KernelService Deployment Strategy

Safe deployment and upgrade plan for the Windows kernel driver.

## Packaging and Signing
- Build driver as signed catalog/INF package; require WHQL signature for production.
- Include versioned INF with strict hardware ids (if any) and service name `SecureKernelSvc`.
- Publish hashes + signature metadata with OTA manifest.

## Installation
- Prefer `pnputil /add-driver /install` for silent install; fall back to service install if needed.
- Run prerequisite checks: secure boot enabled, compatible OS build, matching agent version.
- Register as demand-start service; agent starts service post-boot and monitors heartbeat.

## Upgrade
- Use staged install: add new driver alongside existing, set as next boot target.
- On next boot, verify driver load success and health ping via IOCTL; only then mark active.
- Maintain previous driver for rollback; limit to two installed versions.

## Rollback
- If driver fails to load or health probe fails 3 times, revert to previous signed driver and emit high-severity alert.
- Block further updates until issue acknowledged; require fresh attestation after rollback.

## Observability
- Emit driver version, load status, and health metrics to agent; forward to backend telemetry.
- Log installation attempts with return codes; hash-chain logs to detect tampering.
