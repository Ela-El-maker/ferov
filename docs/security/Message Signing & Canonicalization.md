# Message Signing and Canonicalization

Canonical JSON signing rules applied to commands, telemetry envelopes, and OTA manifests.

## Canonicalization Rules
- UTF-8, no BOM; sort object keys lexicographically; no trailing commas.
- Numbers encoded as strings if precision matters (timestamps, sequence numbers).
- Remove insignificant whitespace; normalize booleans/literals to lowercase.
- Include `nonce`, `sequence`, `issued_at`, `expires_at`, and `kid` in every signed envelope.

## Signing
- Algorithm: Ed25519 for control/telemetry, RSA-PSS for JWT (server auth), EV/WHQL code signing for binaries.
- Payload signed over canonical JSON bytes; signature field is appended separately as `signature`.
- OTA manifest signature includes digest of binaries, SBOM hash, and provenance reference.

## Verification
- Reject messages missing fields or with expired `expires_at` or stale `sequence` (< last seen).
- Verify `kid` exists in JWKS and `not_before` <= `issued_at`; enforce allowed clock skew ±5s.
- For OTA, verify manifest signature before download; verify binary digest after download; abort if mismatch.

## Error Handling
- On signature failure: do not retry automatically; raise high-severity alert; agent enters degraded mode.
- Log verification context (kid, sequence, nonce) without including sensitive payload fields.
