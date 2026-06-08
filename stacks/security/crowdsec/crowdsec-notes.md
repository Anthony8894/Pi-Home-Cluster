# CrowdSec Notes

05/26/2026 - 12:30am

## Why I Switched From Wazuh to CrowdSec

I originally tested Wazuh as a security monitoring tool, but it was too heavy for my Raspberry Pi homelab. I stopped using Wazuh and moved to CrowdSec because it is lighter and better suited for monitoring public web traffic through Traefik.

## Current Status

CrowdSec is installed and running in Docker.

Wazuh is no longer running. This was verified with:

```bash
docker ps -a | grep wazuh
```
= this returned nothing, meaning it wasn't on my pi. 

## Traefik Log Monitoring

Traefik access logs are stored on the host at:
```bash
/opt/traefik/logs/access.log
```

CrowdSec reads those logs from inside the container at:

```bash
/var/log/traefik/access.log
```

The CrowdSec acquisition file is configured like this:

```bash
filenames:
  - /var/log/traefik/access.log
labels:
  type: traefik
```

This was verified with:

```bash
docker exec -it crowdsec cat /etc/crowdsec/acquis.yaml
```

## Traefik Collection

The Traefik collection is installed and enabled.

Verified with:

```bash
docker exec -it crowdsec cscli collections list
```

## Confirmed collection:
```bash
crowdsecurity/traefik enabled
```

## Metrics Verification

CrowdSec is successfully reading and parsing Traefik logs.

Verified with:
```bash
docker exec -it crowdsec cscli metrics
```

Important result:

file:/var/log/traefik/access.log
Lines read: 8.94k
Lines parsed: 8.94k
Lines unparsed: -

This confirms that CrowdSec is reading Traefik logs correctly.

## CrowdSec Console

CrowdSec has been enrolled into the CrowdSec web Console.

Verified with:

```bash
docker exec -it crowdsec cscli console status
```

Current Console status:

custom: enabled
manual: enabled
tainted: enabled
context: enabled
console_management: disabled

The Console is being used to view alerts, decisions, and engine status.

## Alerts Seen

CrowdSec has detected public internet scanning activity against the homelab domain and subdomains.

Examples of detected activity:

http-technology-probing
http-sensitive-files
http-admin-interface-probing
http-cve-probing
http-probing

These are common internet bot scans against public-facing services.

## Current Architecture

Internet traffic
    ↓
Traefik reverse proxy
    ↓
Traefik access logs
    ↓
CrowdSec reads logs
    ↓
CrowdSec detects suspicious behavior
    ↓
CrowdSec Console displays alerts and decisions

## Important Limitation

CrowdSec is currently detecting suspicious traffic, but it is not blocking through Traefik yet.

This was verified with:

docker exec -it crowdsec cscli bouncers list

The bouncer list was empty.

## Next Step

Install and configure the Traefik bouncer.