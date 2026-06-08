# Commands Used - CrowdSec Traefik Bouncer

## Purpose

This file lists the main commands I used while setting up the CrowdSec Traefik bouncer.

The bouncer allows Traefik to communicate with CrowdSec and block suspicious IP addresses before they reach my public Docker services.

---

## Go to the Traefik Folder

```bash
cd /opt/traefik
```

I used this to move into the folder where my Traefik Docker Compose file is stored.

---

## Create a CrowdSec Bouncer Key

```bash
docker exec -it crowdsec cscli bouncers add traefik-bouncer
```

This command creates a new bouncer key inside CrowdSec.

The key is used by Traefik so it can talk to CrowdSec.

Important: the real key should not be uploaded to GitHub.

---

## Edit the Traefik Docker Compose File

```bash
sudo nano docker-compose.yml
```

I used this command to edit my Traefik Docker Compose file.

This is where I added the CrowdSec Traefik bouncer plugin and middleware labels.

---

## Restart Traefik

```bash
docker compose up -d
```

This command restarts Traefik using the updated Docker Compose file.

The `-d` means it runs in the background.

---

## Check Traefik Logs

```bash
docker logs traefik --tail=100
```

This command shows the last 100 lines of the Traefik logs.

I used this to make sure the CrowdSec plugin loaded correctly.

A good sign is seeing:

```text
Loading plugins... plugins=["crowdsec-bouncer"]
Plugins loaded. plugins=["crowdsec-bouncer"]
```

---

## Check for CrowdSec Bouncers

```bash
docker exec -it crowdsec cscli bouncers list
```

This command shows the bouncers registered with CrowdSec.

I used this to confirm that `traefik-bouncer` was created.

---

## Check CrowdSec Decisions

```bash
docker exec -it crowdsec cscli decisions list
```

This command shows the current IP decisions in CrowdSec.

If CrowdSec has banned or flagged an IP, it should show up here.

---

## Check CrowdSec Logs

```bash
docker logs crowdsec --tail=100
```

This command shows the last 100 lines of the CrowdSec logs.

I used this to check if CrowdSec was running correctly.

---

## Connect CrowdSec to the Proxy Network

```bash
docker network connect proxy crowdsec
```

This command connects the CrowdSec container to the same Docker network as Traefik.

This is important because Traefik needs to reach CrowdSec using:

```text
crowdsec:8080
```

If CrowdSec is already connected to the network, Docker may say it already exists. That is fine.

---

## Check Docker Networks

```bash
docker inspect traefik | grep -A 20 Networks
docker inspect crowdsec | grep -A 20 Networks
```

I used these commands to check which Docker networks Traefik and CrowdSec were connected to.

Both containers should be on the `proxy` network.

---

## Search Repo for the Bouncer Key

```bash
grep -R "crowdseclapikey" .
```

I used this before pushing to GitHub to make sure my real CrowdSec bouncer key was not accidentally uploaded.

In GitHub, the key should be replaced with a placeholder like:

```text
CROWDSEC_BOUNCER_KEY_HERE
```

---

## Git Commands Used

```bash
git status
```

Shows which files were changed.

```bash
git add .
```

Adds the changed files to the commit.

```bash
git commit -m "Add CrowdSec Traefik bouncer documentation"
```

Creates a commit with a message.

```bash
git push
```

Uploads the changes to GitHub.

---

## Issue I Ran Into

At first, I used this value:

```text
http://crowdsec:8080
```

Traefik turned it into:

```text
http://http://crowdsec:8080/
```

This caused an error.

The fix was to use:

```text
crowdsec:8080
```

without `http://`.

---

## Final Result

After running these commands and fixing the API host value, the CrowdSec Traefik bouncer plugin loaded successfully.

Traefik can now use CrowdSec decisions to help block suspicious traffic before it reaches my public services.
