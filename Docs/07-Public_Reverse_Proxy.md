# 07 - Public Reverse Proxy (Traefik)

## 📌 Overview
In this project, I configured a public reverse proxy using Traefik to expose services from my homelab to the internet in a secure and organized way.

A reverse proxy acts as a middle layer between incoming internet traffic and internal services. Instead of exposing multiple ports, all traffic flows through a single entry point (HTTP/HTTPS), and Traefik routes requests based on rules such as domain names.

---

## 🧠 Why This Matters
Before using a reverse proxy:
- Services were accessed via IP + port (e.g., 192.168.50.199:3000)
- Not scalable or user-friendly
- Hard to secure

After implementing Traefik:
- Services are accessed via domain names (e.g., grafana.local)
- Centralized routing
- Easier to secure with HTTPS and authentication
- Cleaner architecture

---

## 🏗️ Architecture

Internet
   ↓
Router (Port Forwarding 80/443)
   ↓
Traefik (Reverse Proxy)
   ↓
Docker Containers (Grafana, Dashy, etc.)

---

## ⚙️ Key Components

### Traefik
- Reverse proxy and load balancer
- Automatically detects Docker services
- Uses labels for routing configuration

### Docker
- Runs all services as containers
- Traefik connects to Docker provider

### EntryPoints
- Define ports Traefik listens on (80 = HTTP, 443 = HTTPS)

### Routers
- Define how requests are matched (domain names, paths)

### Services
- Define where traffic is sent (container + port)

### Middleware
- Adds extra functionality (authentication, redirects, etc.)

---

## 🧪 Example Flow

User visits:
http://grafana.local

1. Request hits Traefik
2. Traefik checks router rules
3. Matches "grafana.local"
4. Routes traffic to Grafana container
5. Response is sent back to user

---

## 📦 Example Docker Label

```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.grafana.rule=Host(`grafana.local`)"
  - "traefik.http.services.grafana.loadbalancer.server.port=3000"
