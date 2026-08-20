# Runbook

Quick response steps for common host-level alerts.

## High CPU

1. Open **CPU Metrics**, identify the offending core(s).
2. Correlate with load average and throttling panels.
3. Check **Application Logs** for a runaway process.

## Memory pressure / OOM

1. Open **Memory Metrics**; watch swap usage and OOM events.
2. If swap is saturated, capture the top consumers before restarting.

## Disk saturation

1. Open **Disk I/O**; find the device with high latency or IOPS.
2. Verify free space and inode usage on that device.

## Network anomalies

1. Open **Network Traffic**; look for error/drop spikes per interface.
2. Rule out upstream issues before restarting services.
