# CI/CD Architecture

Pipeline for building, signing, testing, and promoting artifacts.

## Build
- Separate pipelines per component (Laravel, FastAPI, agent, driver, mobile); shared steps for lint/test/build.
- Use cached dependencies with checksum validation; no network access after dependency fetch.
- Generate SBOM and provenance for each build; upload to artifact store.

## Security Gates
- Static analysis (CodeQL/semgrep) for services and agent; driver-specific static analysis.
- Dependency scanning; block on high/critical findings.
- Secrets scanning on every commit.

## Signing
- Artifacts signed via signing service backed by HSM; enforce role separation (builder vs signer).
- Record signing metadata (hash, kid, timestamp) in audit log and OTA manifest.

## Promotion
- Dev → staging → prod workflows require automated tests + manual approval.
- Canary deploys to small slice with automated rollback on health degradation.
- Tag Docker images immutably (`service:version-buildid`); Helm values reference immutable tags.

## Release Outputs
- Publish release notes with changelog, SBOM links, and known issues.
- Notify event bus `release.published` with component, version, and checksum.
