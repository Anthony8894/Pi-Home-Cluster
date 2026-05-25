# Node Exporter

Node Exporter is used to collect basic Linux system metrics from my Raspberry Pi nodes.

It exposes hardware and operating system metrics such as:

- CPU usage
- Memory usage
- Disk usage
- Network usage
- System load
- Uptime

Prometheus connects to Node Exporter on port `9100` and collects these metrics.

## Why I Use It

Node Exporter lets Prometheus monitor each Raspberry Pi in my homelab.

In my setup, Node Exporter runs on:

- `pi-main`
- `pi-worker`

Prometheus then scrapes both devices from the `prometheus.yml` file.

## Port Used

Node Exporter uses port:

```text
9100
```

## Example target:

192.168.50.199:9100
How It Connects to Prometheus

Prometheus uses this config:

scrape_configs:
  - job_name: 'node-exporter'
    static_configs:
      - targets:
        - '192.168.50.199:9100'
        - '192.168.50.149:9100'