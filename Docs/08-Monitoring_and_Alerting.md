# 08 - Monitoring and Alerting

## Overview

This section focuses on implementing monitoring and alerting for my Raspberry Pi home cluster. The goal is to gain visibility into system performance, container health, and service availability, while also learning how to detect and respond to issues in a production-style environment.

Monitoring transforms this homelab from a simple set of running services into an observable and maintainable system.

---

## Objectives

* Understand the purpose of monitoring and observability
* Deploy a lightweight monitoring stack on Raspberry Pi nodes
* Collect system and container metrics
* Visualize data using dashboards
* Monitor service uptime and availability
* Begin implementing basic alerting strategies

---

## Monitoring Stack

The following tools are used in this section:

* **Prometheus** – Metrics collection and storage
* **Grafana** – Dashboard visualization
* **Node Exporter** – Host-level metrics (CPU, RAM, disk, etc.)
* **cAdvisor** – Container-level metrics
* **Uptime Kuma** – Service uptime and availability monitoring

---

## Architecture Overview

Monitoring in this homelab follows this flow:

1. Node Exporter runs on each Raspberry Pi node
2. cAdvisor collects container metrics
3. Prometheus scrapes metrics from exporters
4. Grafana connects to Prometheus for visualization
5. Uptime Kuma monitors service endpoints

This provides both:

* **Metrics-based monitoring** (performance and resource usage)
* **Availability monitoring** (is the service reachable?)

---

## What Is Being Monitored

### Node Health

* CPU usage
* Memory usage
* Disk usage
* System uptime
* Node availability

### Container Health

* Container CPU usage
* Memory consumption
* Container uptime
* Restart behavior

### Service Availability

* Portainer
* Grafana
* Traefik
* Nextcloud
* Other web services

---

## Alerting Goals

The goal of alerting is to detect important issues without creating unnecessary noise.

Initial alerts to implement:

* Node offline (pi-main or pi-worker1)
* High disk usage (80%+)
* Sustained high CPU usage
* Low available memory
* Critical service down (Grafana, Traefik, etc.)
* Container repeatedly restarting

---

## Folder Structure

```bash
stacks/
└── monitoring/
    ├── prometheus/
    ├── grafana/
    ├── node-exporter/
    ├── cadvisor/
    └── uptime-kuma/
```

Each service will have its own configuration and Docker Compose setup.

---

## Deployment Plan

The monitoring stack will be deployed in the following order:

1. Node Exporter (on each node)
2. Prometheus (metrics collection)
3. Grafana (dashboard visualization)
4. cAdvisor (container metrics)
5. Uptime Kuma (service monitoring)

This order ensures that metrics are available before visualization.

---

## Key Concepts Learned

* Difference between **metrics monitoring** and **uptime monitoring**
* How Prometheus scrapes and stores time-series data
* How Grafana visualizes infrastructure metrics
* How exporters provide data from systems and containers
* Importance of observability in real-world environments

---

## Challenges

* Resource limitations on Raspberry Pi hardware
* Avoiding overly complex configurations early on
* Preventing alert fatigue from too many notifications
* Understanding relationships between monitoring components

---

## Future Improvements

* Integrate alert notifications (email, Discord, Telegram)
* Add Prometheus Alertmanager
* Build advanced Grafana dashboards
* Add log monitoring (e.g., Wazuh or Loki)
* Monitor backup status
* Track SSL certificate expiration

---

## Summary

Monitoring and alerting are essential for running a stable and reliable environment. By implementing this stack, I am learning how to observe system behavior, detect issues early, and build a more production-ready homelab.

This section marks the transition from simply running services to actively managing and maintaining infrastructure.


## Current Progress (as of 04/18/2026)

Completed:
- Node Exporter deployed on both nodes
- Prometheus configured and collecting metrics
- Grafana installed and connected to Prometheus
- Node Exporter dashboard imported and working

In Progress:
- cAdvisor (container metrics)
- Uptime Kuma (service monitoring)
- Alerting setup