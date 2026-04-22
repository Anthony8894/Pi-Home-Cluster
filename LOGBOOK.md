# Homelab Logbook

<!-- ## 2026-04-04

### What I did
- Set up Traefik reverse proxy
- Fixed YAML issues
- Got dashboard accessible

### Problems
- YAML errors (indentation)
- Dashboard required login

### What I learned
- YAML is very strict with spacing
- Reverse proxies route traffic using rules/hostnames
- Traefik uses labels heavily for config

### Next step
- Document setup
- Save config
- Understand middleware/auth

-->

## 04/18/2026 

### what I did today:
- Everything below contributed to doc 8 in the document folder
- Installed Prometheus on pi-main to collect metrics
- Configured prometheus.yml to scrape both nodes:
* pi-main
* pi-worker1
- Installed Grafana for visualization
- Connected Grafana to Prometheus as a data source
- Imported Node Exporter dashboard (ID: 1860)
- Verified both nodes are being monitored and displayed in dashboards

### issues I ran into
- Prometheus couldn’t reach pi-main (hostname issue → fixed by using IPs)
- Prometheus container couldn’t access host network (fixed with network_mode: host)
- Couldn’t access Prometheus from browser (UFW firewall blocking port 9090)
- Grafana container kept restarting (volume permission issue → fixed with chown 472:472)

### what I learned
- Prometheus uses a pull model (scrapes targets instead of agents pushing data)
- Docker containers may not resolve hostnames correctly → use IPs when needed
- Difference between bridge networking vs host networking
- How to troubleshoot:
* local vs container vs network issues
- Grafana needs proper file permissions for persistent storage
- Firewalls (UFW) can block services even if they are running correctly

### next steps
- Put Grafana and Prometheus behind Traefik (HTTPS + domain)
- Clean up and document architecture in GitHub README
- Add screenshots of dashboards
- Monitor an actual service (not just system metrics)

## 04/21/2026
### Log Entry - Traefik / Grafana Issue

Today I troubleshooted an issue where I was unable to log into Grafana using my public domain (grafana.anthonylearchive.com). The page would refresh immediately after entering credentials.

I verified that all containers (Traefik, Grafana, Prometheus) were running correctly using `docker ps`. After reviewing the Traefik logs, I noticed multiple 401 Unauthorized responses and that some requests were being routed to the `whoami` container instead of Grafana.

I checked the Docker labels and found that both the `whoami` container and the Grafana container were using the same Host rule (`grafana.anthonylearchive.com`). This caused Traefik to route requests inconsistently between the two services.

I resolved the issue by assigning a unique domain to the `whoami` container (`whoami.anthonylearchive.com`) and restarting Traefik. After the change, Grafana login worked as expected.

I also reset the Traefik dashboard Basic Auth password by generating a new hash using `htpasswd` and updating the configuration.

Issue resolved successfully.