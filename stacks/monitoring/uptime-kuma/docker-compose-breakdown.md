services:

This starts the section where you define your Docker containers/services.

In this file, you only have one service:

  uptime-kuma:

This is the service name. Docker Compose will recognize this container/service as uptime-kuma.

    image: louislam/uptime-kuma:latest

This tells Docker which image to download and run.

In simple terms:

“Download and run the latest Uptime Kuma image.”

Uptime Kuma is the monitoring dashboard that checks whether your websites/services are online.

    container_name: uptime-kuma

This gives the container a fixed name.

Instead of Docker creating a random name, your container will simply be called:

uptime-kuma

That makes it easier to run commands like:

docker logs uptime-kuma
docker restart uptime-kuma
docker stop uptime-kuma
    restart: unless-stopped

This tells Docker:

“Keep this container running unless I manually stop it.”

So if your Raspberry Pi reboots, Docker will automatically start Uptime Kuma again.

If the container crashes, Docker will try to bring it back up.

    ports:
      - "3002:3001"

This maps a port on your Raspberry Pi to a port inside the container.

The format is:

HOST_PORT:CONTAINER_PORT

So this means:

Raspberry Pi port 3002 → Uptime Kuma container port 3001

You can access Uptime Kuma directly on your local network with:

http://192.168.50.199:3002

But inside the container, Uptime Kuma actually runs on:

3001

This part is mainly for direct local access.

    volumes:
      - /mnt/appdata/uptime-kuma:/app/data

This saves Uptime Kuma’s data outside the container.

The format is:

HOST_PATH:CONTAINER_PATH

So this means:

/mnt/appdata/uptime-kuma on your Raspberry Pi

is connected to:

/app/data inside the container

This is important because Uptime Kuma stores things like:

your monitors
your notification settings
your dashboard settings
your login/admin data

Without this volume, you could lose your Uptime Kuma data if the container was deleted.

This line makes the data persistent.

    labels:

Labels are extra instructions for Traefik.

Docker itself does not really care about these labels. Traefik reads them and says:

“Okay, I see this container. Let me expose it through the domain name.”

      - "traefik.enable=true"

This tells Traefik:

“Yes, Traefik should manage this container.”

Without this, Traefik may ignore the container.

      - "traefik.docker.network=proxy"

This tells Traefik which Docker network to use when talking to Uptime Kuma.

Since Traefik is also on the proxy network, Uptime Kuma needs to be on that same network too.

Think of it like this:

Traefik and Uptime Kuma must be in the same Docker “room” so they can talk to each other.

      - "traefik.http.routers.uptime-kuma.rule=Host(`uptime.anthonylearchive.com`)"

This is the domain rule.

It tells Traefik:

“When someone visits uptime.anthonylearchive.com, send them to the Uptime Kuma container.”

This is what connects your subdomain to the service.

      - "traefik.http.routers.uptime-kuma.entrypoints=websecure"

This tells Traefik to use the secure HTTPS entrypoint.

Usually in your Traefik setup:

web = HTTP, port 80
websecure = HTTPS, port 443

So this means Uptime Kuma should be served through HTTPS.

      - "traefik.http.routers.uptime-kuma.tls=true"

This enables TLS/HTTPS for this router.

In simple terms:

“Use HTTPS for this website.”

So instead of:

http://uptime.anthonylearchive.com

you use:

https://uptime.anthonylearchive.com
      - "traefik.http.routers.uptime-kuma.tls.certresolver=myresolver"

This tells Traefik which certificate resolver to use.

In your case, your working resolver is:

myresolver

This is the part that lets Traefik request and manage a Let’s Encrypt SSL certificate for:

uptime.anthonylearchive.com

You previously fixed issues by changing this from letsencrypt to myresolver, so this is correct for your setup.

      - "traefik.http.services.uptime-kuma.loadbalancer.server.port=3001"

This tells Traefik which port to use inside the container.

This is very important.

Even though you mapped this earlier:

ports:
  - "3002:3001"

Traefik does not care about the outside port 3002.

Traefik talks to the container directly through Docker networking, so it needs the internal app port:

3001

So this label means:

“Traefik, when you send traffic to Uptime Kuma, send it to port 3001 inside the container.”

    networks:
      - proxy

This connects the Uptime Kuma container to the Docker network named proxy.

This is required because Traefik is also using that network.

Without this, Traefik may not be able to reach the Uptime Kuma container.

networks:
  proxy:
    external: true

This defines the proxy network.

The important part is:

external: true

That means:

“Do not create a new network. Use an existing Docker network named proxy.”

This is usually the same network your Traefik container is already using.

You can check it with:

docker network ls

You should see something like:

proxy
In plain English

This file says:

Run Uptime Kuma in Docker, save its data to /mnt/appdata/uptime-kuma, expose it locally on port 3002, and let Traefik make it available securely at https://uptime.anthonylearchive.com.

Important ports summary
Item	Port
Uptime Kuma inside the container	3001
Raspberry Pi local access port	3002
Traefik HTTPS access	443
Public domain	uptime.anthonylearchive.com

So these should work:

http://192.168.50.199:3002

and:

https://uptime.anthonylearchive.com
The most important lines

These are the key lines that make the domain work:

- "traefik.http.routers.uptime-kuma.rule=Host(`uptime.anthonylearchive.com`)"
- "traefik.http.routers.uptime-kuma.entrypoints=websecure"
- "traefik.http.routers.uptime-kuma.tls=true"
- "traefik.http.routers.uptime-kuma.tls.certresolver=myresolver"
- "traefik.http.services.uptime-kuma.loadbalancer.server.port=3001"

The easiest way to remember it:

Router = decides which domain goes where.
Service = tells Traefik what internal container port to use.
Network = lets Traefik and the container talk.