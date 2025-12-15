# OTA Pipeline

Publisher workflow for agent and kernel updates.

## Stages
- **Build**: compile agent/driver with reproducible flags; generate SBOM + provenance.
- **Sign**: sign binaries and manifest with OTA key in HSM; include SHA-256 digests and SBOM hash.
- **Promote**: dev → staging → prod with automated tests (install, rollback, WSS reconnect). Promotion requires two approvals.
- **Publish**: upload packages to CDN; publish manifest to FastAPI/Laravel and event `ota.release.published`.

## Rollout Strategy
- Phased rollout percentages (5% → 20% → 50% → 100%) with health gates on failure rate (<2%) and crash-free rate (>98%).
- Region-aware rollouts; can halt per region.
- Staged downloads with bandwidth caps; agent respects `max_concurrent_downloads`.

## Rollback
- Publisher can mark release `recalled`; FastAPI instructs agents to revert to previous version.
- Agents keep last known-good snapshot; rollback logged to compliance + alerting.

## Security
- Manifests include expiry, min agent version, required kernel driver version, and reboot requirements.
- Binary diff generation optional; if used, delta packages signed with same manifest key.
- Store provenance + SBOM in secure bucket with retention; hash logged to audit chain.
