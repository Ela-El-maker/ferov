# Compliance Dashboard

UI and backend behaviors for compliance visibility.

## Features
- Real-time compliance status per device with badges (`compliant`, `non_compliant`, `unknown`, `quarantined`).
- Drill-down per finding: failed rules, timestamps, remediation steps, related alerts.
- Trend charts: compliance over time, top failing rules, time-to-remediate metrics.
- Export: CSV/PDF and webhook export for SIEM.

## Data Pipeline
- FastAPI scheduled job evaluates compliance every 10 minutes and publishes to `compliance.findings.v1`.
- Laravel ingests findings, stores history, and indexes for dashboard queries.
- Alerts generated when `non_compliant` persists >30 minutes or number of affected devices crosses threshold.

## Permissions
- Admins see all; device owners see only their devices; auditors get read-only exports.
- All dashboard actions logged to audit chain with actor and filter parameters.
