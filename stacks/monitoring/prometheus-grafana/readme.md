# Prometheus + Grafana Monitoring Stack

This folder documents my basic homelab monitoring setup using **Prometheus**, **Grafana**, and **Node Exporter**.

## What This Stack Does

This stack helps me monitor my Raspberry Pi homelab.

- **Prometheus** collects system metrics.
- **Node Exporter** runs on each Raspberry Pi and exposes system information.
- **Grafana** displays the metrics in dashboards.

In simple terms:

> Node Exporter gives the data, Prometheus collects the data, and Grafana makes it easy to view.

## Services Used

### Prometheus

Prometheus is the monitoring tool that collects metrics from my homelab devices.

It uses the `prometheus.yml` file to know which devices to monitor.

Current monitored targets:

```yaml
targets:
  - '192.168.50.199:9100'
  - '192.168.50.149:9100'
```
These are my Raspberry Pi nodes running Node Exporter.

## Grafana

Grafana is the dashboard tool.

I use it to view charts and graphs for things like:

- CPU usage
- Memory usage
- Disk usage
- Network activity
- System performance

Grafana is available through Traefik at:

https://grafana.anthonylearchive.com

Folder Structure
prometheus-grafana/
├── docker-compose.yml
├── prometheus.yml
├── README.md
└── docker-compose-breakdown.md

## Important Files
docker-compose.yml

This file starts the Prometheus and Grafana containers.

Both services are kept in one compose file because they work together as part of the same monitoring stack.

## prometheus.yml

This file tells Prometheus what devices to monitor.

Example:

global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node-exporter'
    static_configs:
      - targets:
        - '192.168.50.199:9100'
        - '192.168.50.149:9100'

This means Prometheus checks both Raspberry Pis every 15 seconds.