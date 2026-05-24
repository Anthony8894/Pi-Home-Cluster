Breakdown
services
services:

The services section is where Docker Compose defines the containers that should run.

In this file, there is one service:

dozzle:

That means Docker Compose will create and manage one container for Dozzle.

dozzle
dozzle:

This is the name of the service inside the Docker Compose file.

The service name is mainly used by Docker Compose. It helps identify this specific application in the stack.

image
image: amir20/dozzle:latest

This tells Docker which container image to download and run.

In this case:

amir20/dozzle:latest

means Docker will pull the latest Dozzle image.

Simple meaning:

Use the Dozzle container image.
container_name
container_name: dozzle

This gives the container a simple name.

Instead of Docker creating a longer automatic name, the container will simply be called:

dozzle

This makes commands easier.

Example:

docker logs dozzle
docker restart dozzle
docker ps | grep dozzle
restart
restart: unless-stopped

This tells Docker to automatically restart the container if it crashes or if the Raspberry Pi reboots.

The container will keep restarting unless I manually stop it.

Simple meaning:

Keep Dozzle running unless I intentionally stop it.
Volumes
volumes:
  - /var/run/docker.sock:/var/run/docker.sock:ro
  - dozzle_data:/data

Volumes allow the container to access files or storage outside of the container.

This Dozzle container uses two volume mounts.

Docker Socket Mount
- /var/run/docker.sock:/var/run/docker.sock:ro

This is the most important part of the Dozzle setup.

The Docker socket is how Dozzle can see containers and read their logs.

Simple meaning:

Allow Dozzle to read Docker container information from the host.

The first part:

/var/run/docker.sock

is the Docker socket on the Raspberry Pi.

The second part:

/var/run/docker.sock

is where that same socket appears inside the Dozzle container.

The :ro means:

read-only

This is safer than giving Dozzle full write access.

Important note:

Even with read-only access, the Docker socket should still be treated carefully because it gives Dozzle visibility into Docker containers and logs.

Dozzle Data Volume
- dozzle_data:/data

This gives Dozzle a place to store its own data.

The named volume is:

dozzle_data

Inside the container, that volume is mounted to:

/data

Simple meaning:

Give Dozzle a persistent storage location.
Environment Variables
environment:
  DOZZLE_HOSTNAME: pi-main
  DOZZLE_NO_ANALYTICS: "true"

Environment variables are settings passed into the container.

They allow me to customize how Dozzle behaves.

DOZZLE_HOSTNAME
DOZZLE_HOSTNAME: pi-main

This labels the Dozzle instance as:

pi-main

This is useful because my homelab has more than one Raspberry Pi.

Current nodes:

pi-main
pi-worker

Since this Dozzle container is installed on pi-main, the hostname helps me remember that the logs shown are from pi-main.

Simple meaning:

Show this Dozzle instance as pi-main.
DOZZLE_NO_ANALYTICS
DOZZLE_NO_ANALYTICS: "true"

This disables analytics.

Simple meaning:

Do not send analytics from this Dozzle instance.
Traefik Labels
labels:

Labels are used by Traefik to automatically detect and route traffic to containers.

Since Dozzle is behind Traefik, these labels tell Traefik:

What domain to use
What port Dozzle uses
Whether to use HTTPS
What middleware to apply
Enable Traefik
- "traefik.enable=true"

This tells Traefik to route traffic to this container.

Without this label, Traefik would ignore the Dozzle container.

Simple meaning:

Let Traefik see and use this container.
Docker Network for Traefik
- "traefik.docker.network=proxy"

This tells Traefik which Docker network to use when connecting to Dozzle.

In this homelab, Traefik and public-facing services use the shared Docker network called:

proxy

Simple meaning:

Use the proxy network to connect Traefik to Dozzle.
Router Rule
- "traefik.http.routers.dozzle.rule=Host(`dozzle.anthonylearchive.com`)"

This tells Traefik which domain should go to Dozzle.

Simple meaning:

When someone visits dozzle.anthonylearchive.com, send that traffic to the Dozzle container.

The router name is:

dozzle

The domain is:

dozzle.anthonylearchive.com
Entrypoint
- "traefik.http.routers.dozzle.entrypoints=websecure"

This tells Traefik to use the HTTPS entrypoint.

In this homelab, websecure is the entrypoint for HTTPS traffic, usually port 443.

Simple meaning:

Use HTTPS for Dozzle.
Enable TLS
- "traefik.http.routers.dozzle.tls=true"

This enables TLS for the Dozzle router.

TLS is what gives the site HTTPS encryption.

Simple meaning:

Make Dozzle use HTTPS.
Certificate Resolver
- "traefik.http.routers.dozzle.tls.certresolver=myresolver"

This tells Traefik which Let's Encrypt certificate resolver to use.

In this homelab, the working resolver is:

myresolver

This part is important because some older service labels used:

letsencrypt

but the correct resolver for the current Traefik setup is:

myresolver

Simple meaning:

Use myresolver to request/manage the HTTPS certificate.
Internal Dozzle Port
- "traefik.http.services.dozzle.loadbalancer.server.port=8080"

This tells Traefik what port Dozzle uses inside the container.

Dozzle listens internally on:

8080

Simple meaning:

Traefik should send traffic to port 8080 inside the Dozzle container.

This does not mean I need to expose port 8080 publicly on the Raspberry Pi. Traefik talks to the container through Docker networking.

Basic Auth Middleware
- "traefik.http.routers.dozzle.middlewares=homepage-auth"

This applies the existing Traefik basic authentication middleware.

In this setup, Dozzle reuses the same basic auth middleware as Homepage.

Simple meaning:

Require a username and password before allowing access to Dozzle.

This is important because Dozzle shows container logs, and logs can contain sensitive information.

Dozzle should not be open to the public internet without authentication.

Networks
networks:
  - proxy

This connects the Dozzle container to the Docker network named:

proxy

Traefik also uses this network.

Simple meaning:

Put Dozzle on the same network as Traefik so Traefik can reach it.
External Proxy Network
networks:
  proxy:
    external: true

This means the proxy network already exists outside of this Docker Compose file.

Docker Compose should not create a brand-new network. It should use the existing Traefik network.

Simple meaning:

Use the existing proxy network that Traefik already uses.

If the proxy network does not exist, the stack will fail to start.

To check if it exists:

docker network ls

To create it if needed:

docker network create proxy
Named Volume
volumes:
  dozzle_data:

This defines the named Docker volume used by Dozzle.

The volume is called:

dozzle_data

Docker manages this volume automatically.

Simple meaning:

Create persistent storage for Dozzle.