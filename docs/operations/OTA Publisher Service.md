# OTA Publisher Service

Backend service responsible for preparing and distributing OTA releases.

## Responsibilities
- Generate signed update manifests with SHA-256 digests, SBOM references, and provenance links.
- Manage release channels (dev/staging/prod) and promotion workflows with approvals.
- Produce binary diffs/deltas when applicable; fall back to full packages when delta risk too high.
- Store SBOM and provenance metadata in secure bucket; expose metadata to compliance dashboard.
- Emit events `ota.release.published` and `ota.release.recalled`.

## API Sketch
- `POST /releases`: upload build artifacts, SBOM, metadata; returns `release_id`.
- `POST /releases/{id}/promote`: promote to target channel with rollout percentages.
- `POST /releases/{id}/recall`: mark release recalled, trigger agent rollback.
- `GET /releases/{id}/manifest`: retrieve signed manifest for FastAPI.

## Safety and Rollback
- Promotion gated by automated install tests and crash-free metrics.
- Recalled releases immediately push rollback command; manifests expire after recall.
- Keep immutable audit trail for all promotions with approver ids.
