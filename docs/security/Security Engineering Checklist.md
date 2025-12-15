# Security Engineering Checklist

Minimum SDL requirements for all components before promotion.

## Build and Signing
- Windows agent and driver built reproducibly; EV code signing for user-mode, WHQL-signed driver package (INF/cab).
- OTA manifests signed with dedicated key stored in HSM; SBOM + provenance attached to each release.
- Release artifacts hashed (SHA-256) and published alongside signatures; verification enforced in CI and agent.

## Dependencies and Supply Chain
- Weekly dependency vulnerability scans (Snyk/Dependabot) with blocking severity ≥ high.
- Pin dependencies with checksums; ban transitive downloads during build; vendor critical libraries when possible.
- Container base images scanned and signed (Cosign); admission controller verifies signatures.

## Secrets and Identity
- Rotate JWKS keys every 90 days; maintain kid history.
- Use HSM/KMS for CA keys, JWT signing keys, and OTA manifest keys.
- Enforce mTLS for service-to-service; certificates issued with short lifetimes and CRL/OCSP endpoints.

## CI/CD Gates
- Mandatory unit + integration tests; fuzz on protocol parsers; static analysis (CodeQL) for agent/driver.
- Minimum code review of 2 approvers for security-sensitive changes; enforce branch protection.
- Provenance attestation (SLSA level 2+ target) stored with release metadata.

## Runtime Hardening
- Rate limits on ingress (per IP/user/device) with circuit breakers.
- Strict IAM for cloud resources; no wildcard roles; rotate credentials quarterly.
- Logging with PII redaction; hash-chained audit logs shipped off-host within 60s.

## Incident Response
- Document runbooks for revocation, OTA rollback, and quarantine escalation.
- Security alert routing to on-call with 24/7 coverage; test pager monthly.
