# Performance Requirements

Targets for scale and latency.

- Supported devices: 50k active devices, 10k concurrent WSS connections.
- Command throughput: sustain 2k cmds/sec; P95 latency <300ms; P99 <500ms.
- Telemetry ingest: 5k msgs/sec; retention 30 days in analytics store.
- OTA: CDN supports 2 Gbps aggregate; per-device cap 5 Mbps; rollout concurrency limited to 5% fleet at a time.
- Compliance evaluation job completes within 5 minutes for full fleet; incremental updates every 10 minutes.
- Availability: 99.5% for control plane; 99.9% for OTA artifact hosting.
