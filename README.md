# Rasberry Pi-Home-Cluster

This repository documents my home Raspberry Pi cluster which both runs ubuntu server. My plan is to simulate a small production environment using containerization, monitors, and security tools commonly found in enterprise IT. 
My lab is hands-on, production-minded, and well documented, this serves as a learning environment + portfolio project.

## 🎯 Goals for this project

- Gain hands-on experience with **Linux system administration**
- Design and operate a **multi-node containerized environment**
- Practice **monitoring, logging, and security operations**
- Document infrastructure clearly and professionally
- Build practical experience aligned with **sysadmin, infrastructure, and cybersecurity roles**

---

## 🧱 Cluster Overview

- **Nodes:**  
  - `pi-main` (Manager)  
  - `pi-worker` (Worker)

- **Operating System:** Ubuntu Server LTS (fresh install)
- **Architecture:** ARM64 (Raspberry Pi)

> This cluster simulates a small production setup with separation of roles, centralized management, and monitoring.

---

## 🧰 Technologies & Tools

### Infrastructure & Containers
- Docker Engine
- Docker Swarm
- Portainer CE

### Monitoring & Observability
- Prometheus
- Grafana
- Node Exporter
- cAdvisor
- Uptime Kuma

### Security
- Wazuh SIEM
- Log collection & alerting

### Applications
- Nextcloud (self-hosted file service)

### Networking & Access
- SSH (key-based authentication)
- Firewall (UFW)
- Secure remote access (Tailscale / WireGuard)

---

## 🗺️ Architecture

> Diagrams and visuals are available in the `architecture/` directory.

- Multi-node Docker Swarm
- Overlay networking for services
- Centralized monitoring and logging
- Persistent volumes for stateful services

---

## 📂 Repository Structure

```text
pi-home-cluster/
├── README.md
├── architecture/
│   ├── network-diagram.png
│   ├── service-diagram.png
│   └── cluster-overview.md
├── docs/
│   ├── 01-initial-setup.md
│   ├── 02-networking.md
│   ├── 03-docker-portainer.md
│   ├── 04-docker-swarm.md
│   ├── 05-reverse-proxy.md
│   ├── 06-monitoring.md
│   ├── 07-security.md
│   ├── 08-backups.md
│   └── 09-troubleshooting.md
├── stacks/
│   ├── infrastructure/
│   ├── monitoring/
│   ├── security/
│   └── apps/
├── screenshots/
└── LICENSE
