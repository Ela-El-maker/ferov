# Policy Management Portal

UI/admin console requirements for managing policies.

## Capabilities
- Create/edit policy bundles with schema-aware editor and linting.
- Version history with diffs, preview impact (affected commands/devices), and rollback to prior versions.
- Workflow: draft → review → publish with two-person approval for high-risk changes.
- Policy testing sandbox that replays historical telemetry to validate decisions.

## Publishing
- Publish triggers policy validation API and emits `policy.updated` event with hash/version.
- Supports staged rollout by tenant/device group; automatic revert on error rate threshold.
- Keep audit trail of editors, approvers, and publication timestamps.

## Access Control
- RBAC: policy authors, reviewers, and auditors with least privilege.
- All actions logged to audit chain and exportable for compliance reports.
