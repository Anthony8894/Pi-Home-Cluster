# Dozzle

## Overview

Dozzle is a lightweight web dashboard used to view Docker container logs.

In this homelab, Dozzle is used to make troubleshooting easier. Instead of SSHing into the Raspberry Pi and running `docker logs` manually, I can open Dozzle in a browser and view logs from my containers.

Dozzle is currently running on:

```text
pi-main
```

Access URL:

https://dozzle.anthonylearchive.com
Why I Added Dozzle

I added Dozzle because many of my homelab services run in Docker, and checking logs is a common troubleshooting step.

Before Dozzle, I would check logs with commands like:

docker logs traefik --tail=50
docker logs vaultwarden --tail=50
docker logs homepage --tail=50

Dozzle makes this easier by giving me one web interface to view logs for multiple containers.

Services I Can Monitor

Dozzle can help me check logs for services such as:

Traefik
CrowdSec
Vaultwarden
Homepage
Uptime Kuma
Nextcloud
Other Docker containers on pi-main
Current Setup

Dozzle is deployed with Docker Compose.

It is exposed through Traefik using the subdomain:

dozzle.anthonylearchive.com

It uses HTTPS through Traefik and is protected with basic authentication.

This is important because container logs can contain sensitive troubleshooting information.

Important Security Note

Dozzle uses the Docker socket to read container logs:

- /var/run/docker.sock:/var/run/docker.sock:ro

The :ro means the socket is mounted as read-only.

Even though it is read-only, the Docker socket should still be protected because it gives Dozzle visibility into Docker containers.

For this reason, Dozzle is:

Behind Traefik
Protected with basic auth
Using HTTPS
Used only as a log viewer
pi-main vs pi-worker

Dozzle currently only shows containers from:

pi-main

This is expected because Dozzle reads logs from the local Docker socket on the machine where it is installed.

Since this Dozzle instance is installed on pi-main, it only sees containers running on pi-main.

It does not automatically show containers from:

pi-worker

To view logs from pi-worker, I would need to either install another Dozzle instance on pi-worker or configure Dozzle for remote Docker hosts.

For now, I am keeping Dozzle only on pi-main.

Folder Location

Server path:

/opt/stacks/observability/dozzle

Repo path:

stacks/observability/dozzle
Useful Commands

Start Dozzle:

docker compose up -d

Check if Dozzle is running:

docker ps | grep dozzle

View Dozzle logs:

docker logs dozzle --tail=50

Restart Dozzle:

docker restart dozzle

Stop Dozzle:

docker compose down