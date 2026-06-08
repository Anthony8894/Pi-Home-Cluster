# CrowdSec - Lightweight Security Monitoring

## Overview

CrowdSec was added to my homelab as a lightweight security monitoring tool.

I originally tested Wazuh, but it was too resource-heavy for my Raspberry Pi 5 setup. CrowdSec is a better fit for now because it can monitor logs, detect suspicious activity, and run with fewer resources.

In this setup, CrowdSec reads **Traefik access logs** to monitor traffic hitting my public homelab services.

---

## Why I Added CrowdSec

My homelab has several services exposed through Traefik using HTTPS subdomains, such as:

- Homepage
- Grafana
- Uptime Kuma
- Vaultwarden
- Nextcloud

Since these services are reachable through the internet, I wanted better visibility into the traffic coming into my reverse proxy.

CrowdSec helps me detect things like:

- HTTP probing
- Web vulnerability scanning
- Suspicious user agents
- Brute-force behavior
- Known CVE probing attempts
- Repeated suspicious requests

---

## Current Setup

CrowdSec is running in Docker on my Raspberry Pi.

It is currently configured in **detection-only mode**.

That means CrowdSec can read logs and detect suspicious behavior, but it is **not blocking traffic yet**.

Current traffic flow:

```text
Internet
   |
Router
   |
Traefik Reverse Proxy
   |
Homelab Services
   |
Traefik Access Logs
   |
CrowdSec Reads Logs
```

Traefik Access Log Path

Traefik writes access logs on the host at:

/opt/traefik/logs/access.log

Inside the CrowdSec container, that log file is mounted as:

/var/log/traefik/access.log

CrowdSec uses this file to analyze HTTP traffic going through Traefik.