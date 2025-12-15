# Infrastructure Architecture

High-level deployment model.

- **Environments**: dev, staging, prod with isolated VPCs/VNETs and separate CA roots.
- **Kubernetes**: runs Laravel API, FastAPI, and supporting services; Helm charts per env; horizontal autoscaling on CPU/P99 latency.
- **Data stores**: MySQL in HA (primary/replica), Redis cluster for cache/queues, Kafka/Redis Streams for events.
- **Networking**: API gateway/WAF fronting Laravel and FastAPI; private service mesh with mTLS; egress restricted via NAT/egress gateway.
- **Secrets**: KMS/Key Vault for app secrets and signing keys; sealed secrets for k8s.
- **Artifact storage**: Signed Docker images in private registry; OTA artifacts and manifests in versioned object storage with CDN distribution.
- **Monitoring**: Prometheus, Grafana, Loki/ELK, alertmanager integrated with on-call.
