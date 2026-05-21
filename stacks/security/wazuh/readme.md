# Wazuh Evaluation

## Purpose

I tested Wazuh as a security monitoring platform for my homelab. The goal was to explore SIEM concepts, endpoint monitoring, log collection, and security visibility.

## Why I Removed It

Wazuh is powerful, but it was too resource-heavy for my Raspberry Pi 5 8GB setup. The stack includes multiple components such as the manager, indexer, and dashboard, which require more memory and CPU than I wanted to dedicate to one service.

## What I Learned

- SIEM tools collect and analyze security events.
- Wazuh can monitor endpoints, logs, vulnerabilities, and file integrity.
- Not every enterprise tool is a good fit for low-power homelab hardware.
- Resource planning matters when choosing security tools.

## Decision

For now, I decided to remove Wazuh and focus on lighter monitoring tools such as:

- Uptime Kuma for service availability
- Grafana for dashboards
- Traefik logs for reverse proxy visibility
- Docker logs for container troubleshooting
- crowdsec

## Future Plan

I may revisit Wazuh later on a stronger server, such as a Proxmox host, dedicated mini PC, or virtual machine with more CPU and memory.