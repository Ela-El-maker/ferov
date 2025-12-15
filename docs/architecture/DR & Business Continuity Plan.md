# DR and Business Continuity Plan

Guidelines for surviving regional failures and data loss.

## Objectives
- RPO: 15 minutes for databases; 5 minutes for event bus offsets.
- RTO: 1 hour for control plane (Laravel/FastAPI); 4 hours for OTA publisher.

## Backups and Restore
- MySQL: point-in-time recovery with binlogs; snapshots every 15 minutes; weekly restore tests.
- Redis: AOF + daily snapshots; replicate across AZs.
- Kafka/Redis Streams: mirror topics to secondary cluster; retain 30 days.
- Object storage (artifacts, SBOMs): versioned buckets with cross-region replication.

## Failover
- Active/active for web tier via global load balancer; active/passive for DB with automated promotion.
- Event bus consumers restart from mirrored cluster using stored offsets.
- DNS failover for API endpoints with health checks.

## Reconciliation
- After failover, replay events from last checkpoint; run policy cache rebuild and compliance reevaluation.
- Verify certificate and revocation caches refreshed in new region.

## Testing
- Quarterly game days covering region loss, DB corruption, and revoked signing key drills.
- Documented runbooks with owners; postmortems required for failures.
