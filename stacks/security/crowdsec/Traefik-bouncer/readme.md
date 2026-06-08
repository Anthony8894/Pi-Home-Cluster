# CrowdSec Traefik Bouncer

## What This Is

This folder documents how I connected **CrowdSec** to **Traefik** using the CrowdSec Traefik bouncer plugin.

CrowdSec watches logs and looks for suspicious activity. The bouncer connects CrowdSec to Traefik so Traefik can block bad IP addresses before they reach my websites.

## Why I Added This

Before adding the bouncer, CrowdSec could detect suspicious traffic, but it was not actively blocking anything through Traefik.

After adding the bouncer, Traefik can check with CrowdSec and block IPs that CrowdSec has flagged.

This makes my homelab more secure because my public services are not just being monitored — they can also be protected.

## Simple Explanation

The traffic flow looks like this:

```text
Internet
  ↓
Traefik
  ↓
CrowdSec Bouncer Plugin
  ↓
Docker Service
```

If the visitor is normal, Traefik lets them through.

If the visitor is on CrowdSec's block list, Traefik can deny the request.

## Services Used

* Raspberry Pi
* Docker
* Traefik
* CrowdSec
* CrowdSec Traefik Bouncer Plugin

## What I Changed

I updated my Traefik Docker Compose file and added the CrowdSec bouncer plugin.

I added these plugin lines under the Traefik `command:` section:

```yaml
- "--experimental.plugins.crowdsec-bouncer.modulename=github.com/maxlerebourg/crowdsec-bouncer-traefik-plugin"
- "--experimental.plugins.crowdsec-bouncer.version=v1.6.0"
```

Then I added the CrowdSec bouncer middleware using Traefik labels:

```yaml
- "traefik.http.middlewares.crowdsec-bouncer.plugin.crowdsec-bouncer.enabled=true"
- "traefik.http.middlewares.crowdsec-bouncer.plugin.crowdsec-bouncer.crowdsecmode=stream"
- "traefik.http.middlewares.crowdsec-bouncer.plugin.crowdsec-bouncer.crowdseclapikey=CROWDSEC_BOUNCER_KEY_HERE"
- "traefik.http.middlewares.crowdsec-bouncer.plugin.crowdsec-bouncer.crowdseclapihost=crowdsec:8080"
```

The real bouncer key should not be uploaded to GitHub.

## Creating the Bouncer Key

I created the bouncer key with this command:

```bash
docker exec -it crowdsec cscli bouncers add traefik-bouncer
```

CrowdSec gives a key after running this command.

That key is used by Traefik so it can talk to CrowdSec.

## Important Note About the API Host

At first, I used this:

```yaml
crowdseclapihost=http://crowdsec:8080
```

This caused an error because Traefik turned it into:

```text
http://http://crowdsec:8080/
```

The fix was to remove `http://` and use this instead:

```yaml
crowdseclapihost=crowdsec:8080
```

## Applying the Bouncer to a Router

To use the bouncer on a Traefik router, I added it as middleware.

Example:

```yaml
- "traefik.http.routers.traefik.middlewares=dashboard-auth,crowdsec-bouncer"
```

If a service only needs the CrowdSec bouncer, it can use:

```yaml
- "traefik.http.routers.SERVICE-NAME.middlewares=crowdsec-bouncer"
```

If a service already has another middleware, I can add both separated by a comma.

Example:

```yaml
- "traefik.http.routers.SERVICE-NAME.middlewares=existing-auth,crowdsec-bouncer"
```

## Restarting Traefik

After making changes, I restarted Traefik:

```bash
cd /opt/traefik
docker compose up -d
```

## Checking Logs

To check if the plugin loaded correctly:

```bash
docker logs traefik --tail=100
```

A good sign is seeing something like:

```text
Loading plugins... plugins=["crowdsec-bouncer"]
Plugins loaded. plugins=["crowdsec-bouncer"]
```

## Useful Commands

List CrowdSec bouncers:

```bash
docker exec -it crowdsec cscli bouncers list
```

List CrowdSec decisions:

```bash
docker exec -it crowdsec cscli decisions list
```

Check Traefik logs:

```bash
docker logs traefik --tail=100
```

Check CrowdSec logs:

```bash
docker logs crowdsec --tail=100
```

## Current Status

The CrowdSec Traefik bouncer plugin is installed and working.

Traefik is now able to use CrowdSec decisions to help protect public-facing services in my homelab.

## Future Improvements

* Add the CrowdSec bouncer middleware to more services
* Document which services are protected
* Add screenshots of the bouncer working
* Safely test a temporary ban
* Fix any old Traefik labels still using the wrong certificate resolver
* Continue improving the security documentation for the homelab
