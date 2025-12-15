# Certificate Lifecycle

End-to-end handling of device, service, and signing certificates.

## Issuance
- Root CA offline; intermediate CAs per environment (dev/staging/prod) stored in HSM/KMS.
- Device CSR generated on agent; private key stored with DPAPI protection; FastAPI proxies CSR to Laravel CA.
- Service mTLS certs issued with 30-day validity and SANs for service DNS.

## Renewal
- Agents renew at T-7 days; FastAPI enforces renewal on connect if expiring within 72h.
- Services auto-renew via CSR job; hot-reload certs without downtime.
- JWKS keys rotated every 90 days with overlap: old keys valid for 48h grace.

## Revocation
- CRL published every 30 minutes; OCSP responders stapled where possible.
- Revocation events pushed to event bus; agents must fetch updates within 5 minutes.
- On revocation, device moves to quarantine and must re-enroll with new CSR.

## Storage and Auditing
- Keys never leave host; access via DPAPI (agent) or KMS (services).
- All issuance/renewal/revocation logged with request id, device id, operator id, and reason.
- Maintain certificate inventory with status, expiry, and last-checked timestamp; export to compliance dashboard.
