# Vaultwarden Docker Compose Breakdown

This Docker Compose file runs **Vaultwarden**, a lightweight self-hosted password manager that works like Bitwarden.

Vaultwarden is being hosted behind **Traefik**, which allows it to be accessed through:

```text
https://vaultwarden.anthonylearchive.com
```

---

## Full Docker Compose File

```yaml
services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: unless-stopped
    volumes:
      - ./vw-data:/data
    environment:
      DOMAIN: "https://vaultwarden.anthonylearchive.com"
      WEBSOCKET_ENABLED: "true"
      ROCKET_ADDRESS: "0.0.0.0"
      ROCKET_PORT: "80"
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.vaultwarden.rule=Host(`vaultwarden.anthonylearchive.com`)"
      - "traefik.http.routers.vaultwarden.entrypoints=websecure"
      - "traefik.http.routers.vaultwarden.tls=true"
      - "traefik.http.routers.vaultwarden.tls.certresolver=letsencrypt"
      - "traefik.http.services.vaultwarden.loadbalancer.server.port=80"
    networks:
      - proxy

networks:
  proxy:
    external: true
```

---

# Breakdown

## services:

```yaml
services:
```

This section is where all containers are defined.

In this file, there is one service:

```yaml
vaultwarden:
```

This means Docker will create and run a container for Vaultwarden.

---

## image:

```yaml
image: vaultwarden/server:latest
```

This tells Docker which image to download and run.

In this case, it downloads the latest Vaultwarden server image.

---

## container_name:

```yaml
container_name: vaultwarden
```

This sets the container name to:

```text
vaultwarden
```

This makes it easier to manage with commands like:

```bash
docker logs vaultwarden
docker restart vaultwarden
docker stop vaultwarden
```

---

## restart:

```yaml
restart: unless-stopped
```

This tells Docker to automatically restart Vaultwarden if it crashes or if the Raspberry Pi reboots.

It will only stay stopped if I manually stop it.

---

## volumes:

```yaml
volumes:
  - ./vw-data:/data
```

This saves Vaultwarden’s data outside of the container.

The local folder:

```text
./vw-data
```

is connected to this folder inside the container:

```text
/data
```

This is important because it stores things like:

* user accounts
* passwords
* database files
* Vaultwarden settings

If the container is deleted or recreated, the data will still remain in the `vw-data` folder.

---

# Environment Variables

## DOMAIN

```yaml
DOMAIN: "https://vaultwarden.anthonylearchive.com"
```

This tells Vaultwarden what public URL it is using.

This is important because Vaultwarden needs to know its correct domain for web access, login, and links.

---

## WEBSOCKET_ENABLED

```yaml
WEBSOCKET_ENABLED: "true"
```

This enables websocket support.

Websockets help with real-time syncing between Vaultwarden and Bitwarden apps or browser extensions.

---

## ROCKET_ADDRESS

```yaml
ROCKET_ADDRESS: "0.0.0.0"
```

This tells Vaultwarden to listen on all network interfaces inside the container.

In simple terms, it allows Vaultwarden to accept traffic from Traefik.

---

## ROCKET_PORT

```yaml
ROCKET_PORT: "80"
```

This tells Vaultwarden to run on port 80 inside the container.

Traefik will forward traffic to this internal port.

---

# Traefik Labels

Traefik uses labels to know how to route traffic to this container.

## Enable Traefik

```yaml
- "traefik.enable=true"
```

This tells Traefik to manage this container.

Without this label, Traefik would ignore Vaultwarden.

---

## Router Rule

```yaml
- "traefik.http.routers.vaultwarden.rule=Host(`vaultwarden.anthonylearchive.com`)"
```

This tells Traefik:

```text
When someone visits vaultwarden.anthonylearchive.com, send the traffic to the Vaultwarden container.
```

---

## EntryPoint

```yaml
- "traefik.http.routers.vaultwarden.entrypoints=websecure"
```

This tells Traefik to use the secure HTTPS entrypoint.

Usually, `websecure` means port 443.

---

## TLS Enabled

```yaml
- "traefik.http.routers.vaultwarden.tls=true"
```

This enables HTTPS for Vaultwarden.

---

## Certificate Resolver

```yaml
- "traefik.http.routers.vaultwarden.tls.certresolver=letsencrypt"
```

This tells Traefik which certificate resolver to use for SSL certificates.

In my Traefik setup, I should make sure this matches the resolver name used in the main Traefik compose file.

For example, if my Traefik resolver is named:

```text
myresolver
```

then this line should be:

```yaml
- "traefik.http.routers.vaultwarden.tls.certresolver=myresolver"
```

---

## Internal Container Port

```yaml
- "traefik.http.services.vaultwarden.loadbalancer.server.port=80"
```

This tells Traefik that Vaultwarden is listening on port 80 inside the container.

Even though users access the site through HTTPS, Traefik talks to the container using port 80 internally.

---

# Networks

## proxy network

```yaml
networks:
  - proxy
```

This connects the Vaultwarden container to the external Docker network named:

```text
proxy
```

This is important because Traefik also uses the same network.

If Traefik and Vaultwarden are not on the same Docker network, Traefik may not be able to reach Vaultwarden.

---

## external network

```yaml
networks:
  proxy:
    external: true
```

This means the `proxy` network already exists and was created outside of this compose file.

This compose file will not create the network.

The network can be created with:

```bash
docker network create proxy
```

---

# Simple Summary

This compose file does the following:

* Runs Vaultwarden as a Docker container
* Saves Vaultwarden data in the `vw-data` folder
* Allows Vaultwarden to restart automatically
* Connects Vaultwarden to Traefik
* Makes Vaultwarden available at `vaultwarden.anthonylearchive.com`
* Enables HTTPS through Traefik
* Uses the external Docker network named `proxy`

---

# Useful Commands

Start Vaultwarden:

```bash
docker compose up -d
```

Stop Vaultwarden:

```bash
docker compose down
```

Restart Vaultwarden:

```bash
docker restart vaultwarden
```

View logs:

```bash
docker logs vaultwarden
```

View live logs:

```bash
docker logs -f vaultwarden
```

Check if the container is running:

```bash
docker ps
```

---

# Notes

Vaultwarden is important because it stores passwords.

The `vw-data` folder should not be deleted unless I intentionally want to remove the Vaultwarden data.

The Vaultwarden site should only be accessed over HTTPS.

If the site does not load, I should check:

* Traefik is running
* Vaultwarden is running
* DNS points to the correct public IP
* The `proxy` Docker network exists
* The Traefik certificate resolver name is correct
