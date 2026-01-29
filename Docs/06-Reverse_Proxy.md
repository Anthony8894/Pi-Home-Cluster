# 05 — Reverse Proxy (Single-Node Traefik)

This document covers the setup of a **single-node reverse proxy** using **Traefik** on the manager Raspberry Pi (`pi-main`).

The goal is to establish a clean, scalable entry point for internal services before deploying applications such as Portainer, Grafana, or Pi-hole.

This setup intentionally starts **minimal** and is expanded later.

I understand Pi-hole makes it a whole lot easier, but managing hosts is definitely someone should be exposed to. I will enable Pi-hole under portainer in the future to make the process a bit easier! 

---

## What Is a Reverse Proxy?

A reverse proxy acts as a **single front door** for multiple internal services.

Instead of accessing services using IP addresses and ports like:

```c
192.168.50.199:9000
192.168.50.199:8080
```

A reverse proxy allows access via hostnames such as:

portainer.lab.local

grafana.lab.local

The reverse proxy:

- Listens on a single port (HTTP 80 for now)
- Inspects incoming requests
- Forwards traffic to the correct internal service based on hostname rules

This mirrors how production environments are structured.

---

## Why Traefik?

Traefik was chosen because it:

- Integrates directly with Docker
- Uses container labels for routing (no manual config reloads)
- Scales cleanly as services are added
- Is widely used in real infrastructure environments

---

## Environment Overview

- **Node:** `pi-main` (manager)
- **OS:** Ubuntu Server
- **Container runtime:** Docker
- **Reverse proxy:** Traefik (single-node, standalone)
- **Networking:** Docker bridge network (`proxy`)
- **TLS:** Not enabled yet (added later)

---

## Network Design

[ Client Browser ]

|

HTTP :80

|

Traefik

|

Docker Network (proxy)

|

Internal Services

Only Traefik exposes a port to the host.

All services remain internal to Docker.

---

## Step 1 — Create Proxy Network

```bash
docker network create proxy

```

![image1](/Screenshots/Reverse_Proxy/01-Reverse_prox.png)

Verification:

```bash
docker networkls | grep proxy

```

## Step 2 — Traefik + Test Service (whoami)

A minimal Traefik deployment is used, along with a lightweight test service (`whoami`) to verify routing.

### Directory

```bash
sudomkdir -p /opt/traefik
cd /opt/traefik

```

### `docker-compose.yml`

```yaml
services:
traefik:
image:traefik:v3.0
container_name:traefik
restart:unless-stopped

command:
-"--api.dashboard=true"
-"--api.insecure=false"
-"--providers.docker=true"
-"--providers.docker.exposedbydefault=false"
-"--entrypoints.web.address=:80"
-"--log.level=INFO"

ports:
-"80:80"

volumes:
-/var/run/docker.sock:/var/run/docker.sock:ro

networks:
-proxy

labels:
-"traefik.enable=true"
-"traefik.http.routers.traefik.rule=Host(`traefik.lab.local`)"
-"traefik.http.routers.traefik.entrypoints=web"
-"traefik.http.routers.traefik.service=api@internal"

whoami:
image:traefik/whoami:latest
container_name:whoami
restart:unless-stopped
networks:
-proxy
labels:
-"traefik.enable=true"
-"traefik.http.routers.whoami.rule=Host(`whoami.lab.local`)"
-"traefik.http.routers.whoami.entrypoints=web"
-"traefik.http.services.whoami.loadbalancer.server.port=80"

networks:
proxy:
external:true

```

![image2](/Screenshots/Reverse_Proxy/02-Reverse_prox.png)

Make sure to paste and save this in the nano file. 

CTRL + O 

ENTER

CTRL + X

## Step 3 — Docker Compose Prerequisite (Important)

On a fresh Ubuntu Server install, Docker **does not include** Docker Compose by default.

### Issue Encountered

```
unknown shorthand flag: 'd' in -d

```

and:

```
Command 'docker-compose' not found

```

### Root Cause

- Docker Engine was installed
- Docker Compose v2 plugin was missing
- `docker compose` requires `docker-compose-plugin`

### Resolution

Docker’s official repository was added and the Compose plugin installed:

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg

sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg |sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudochmod a+r /etc/apt/keyrings/docker.gpg

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudotee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-compose-plugin

```

## Step 4 — Start Traefik

```bash
cd /opt/traefik
docker compose up -d

```

Verification:

```bash
docker ps

```

Expected containers:

- `traefik`
- `whoami`

## Step 5 — DNS / Host Resolution

Because Pi-hole is not yet installed, hostnames were resolved using a local hosts file.

### Old Entries Cleanup

Hostnames from a previous Pi setup were removed to avoid false positives.

### Active Entries Used

```
192.168.50.199 whoami.lab.local
192.168.50.199 traefik.lab.local

```

Make sure to update your local hosts. If you are on windows, open notepad as an administrator. Open this file: C:\Windows\System32\drivers\etc\hosts

input these lines at the bottom of the page:

192.168.50.199 whoami.lab.local
192.168.50.199 traefik.lab.local

![image3](/Screenshots/Reverse_Proxy/03-Reverse_prox.png)

Save and then exit. 

Now you should be able to open them up as is. 

### macOS / Linux

Run:

```bash
sudo nano /etc/hosts

```

Add:

```
192.168.50.199 whoami.lab.local
192.168.50.199 traefik.lab.local

```

Save, then open the same URLs.

## Troubleshooting Summary

| Issue | Cause | Resolution |
| --- | --- | --- |
| `docker compose` fails | Compose plugin missing | Installed `docker-compose-plugin` |
| `docker-compose` not found | Deprecated v1 | Used Compose v2 |
| Containers not starting | Compose never ran | Re-ran after plugin install |
| Hostnames resolving incorrectly | Old hosts entries | Removed unused entries |

---

## Key Takeaways

- Reverse proxies should be deployed **before** services
- Docker Compose v2 is required on modern systems
- DNS misconfiguration can silently break routing
- Debugging steps are critical documentation, not noise
