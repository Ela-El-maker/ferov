# Agent Self-Recovery Behavior

Ordered recovery loop the agent runs when health checks fail.

1) **Health snapshot**: capture current versions, policy hash, last successful attestation, last OTA result, and log pointers. Do not proceed if disk full >90%.
2) **Connectivity check**: test DNS + HTTPS reachability to FastAPI and Laravel; if failing, switch to offline queue mode with capped buffer (200 commands max).
3) **Time sync**: run Windows time sync; if drift persists >5s, request NTP from backend and stay in limited command set.
4) **Certificate path**: validate device cert expiry; if <72h or invalid, request renewal. If renewal fails, enter quarantine.
5) **Revocation + policy refresh**: fetch revocation lists, JWKS, and latest policy bundle; verify signatures and update caches.
6) **KernelService check**: perform IOCTL ping; if failing, restart service once then fall back to user-mode where safe; mark compliance non_compliant until restored.
7) **Data integrity**: verify local DB checksum; on corruption restore from last snapshot or re-enroll; persist event.
8) **Retry strategy**: exponential backoff with jitter up to 10 minutes; cap total recovery loop to 30 minutes before surfacing to user.
9) **Exit**: rerun compliance evaluation; if still failing, request quarantine and display remediation steps.
