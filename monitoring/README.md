# Monitoring

Core infrastructure monitoring dashboards: system, CPU, memory, disk I/O and
network traffic.

This folder is a good place to look first when investigating a host-level
incident. Start with **System Overview** for a top-down view, then drill into
the resource-specific dashboards.

## Dashboards

| Dashboard | Purpose |
|-----------|---------|
| System Overview | Single-pane summary of host health |
| CPU Metrics | Per-core utilization, load average, throttling |
| Memory Metrics | Usage, cache, swap, OOM events |
| Disk I/O | Throughput, IOPS, latency per device |
| Network Traffic | Bandwidth, packets, errors, drops |

## Conventions

- Dashboards are provisioned from this repository via Git Sync.
- Edit the JSON files here; changes flow back to Grafana on the next sync.
- See **Contributing** for the review process and **Security** for reporting
  sensitive issues.
