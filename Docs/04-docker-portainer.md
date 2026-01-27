# Docker & Portainer

This document covers the installation and configuration of **Docker** and **Portainer**, which form the core container management platform for this cluster.

Docker is used to run and isolate services, while Portainer provides a centralized web-based interface for managing containers, stacks, volumes, and nodes.

---

## Objectives

- Install Docker Engine on all cluster nodes
- Enable non-root container management
- Deploy Portainer for centralized visibility and control
- Establish a consistent method for deploying services

---

## Why Docker

Docker is used to:

- Standardize service deployments
- Isolate applications from the host OS
- Simplify upgrades and rollbacks
- Reduce configuration drift between nodes

This approach closely mirrors how services are deployed in modern infrastructure environments.

---

## Installing Docker Engine

Docker is installed on each Raspberry Pi running Ubuntu Server LTS.

```bash
sudo apt update
sudo apt install docker.io -y

```

**Enable and start Docker:**

```bash
**sudo systemctlenable docker
sudo systemctl start docker**

```

**Verify Docker is running:**

```bash
**docker version
docker ps**

```

## Configure Docker User Permissions (ALL NODES)

To avoid running Docker commands as root, add the administrative user to the `docker` group.

Run on **both nodes**:

```bash
sudo usermod -aG docker piborgergade

```

Make sure to do it for all of the nodes. There should be no output for all. 

⚠️ Log out and back in for the group change to take effect.

Verify access:

```bash
docker ps

```

## Install Portainer Server (MANAGER NODE ONLY)

The following steps are run **only on the manager node (`pi-main`)**.

### Create Persistent Volume

```bash
docker volume create portainer_data

```

![image.png1](/Screenshots/Docker-Portainer/01-Dokcer_Portainer.png)

### Deploy Portainer Server

```bash
docker run -d \
  -p 9000:9000 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce

```

![image.png2](/Screenshots/Docker-Portainer/02-Dokcer_Portainer.png)


Portainer Server will be accessible at:

```
http://<pi-main-ip>:9000

```

Type this in your browser. Make sure port 9000 is included. 

![image.png3](/Screenshots/Docker-Portainer/03-Dokcer_Portainer.png)

## Install Portainer Agent (WORKER NODE ONLY)

The following steps are run **only on the worker node (`pi-worker`)**.

### Deploy Portainer Agent

```bash
docker run -d \
  -p 9001:9001 \
  --name portainer_agent \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/docker/volumes:/var/lib/docker/volumes \
  portainer/agent

```

The agent allows the Portainer Server on `pi-main` to securely manage the worker node.

![image.png4](/Screenshots/Docker-Portainer/04-Dokcer_Portainer.png)


## Verify Cluster Visibility (MANAGER NODE)

From the Portainer web UI:

1. Log in to Portainer
2. Navigate to **Environments**
3. Confirm both `pi-main` and `pi-worker` are visible

This confirms successful server–agent communication.

![image.png5](/Screenshots/Docker-Portainer/05-Dokcer_Portainer.png)


shows both the manager pi and the worker pi. Local is my manager pi in this case. 

If the worker pi doesn’t show up you can always add it by clicking “add environment”. 

![image.png6](/Screenshots/Docker-Portainer/06-Dokcer_Portainer.png)


Click the different environments and make sure you see different volumes and stacks.
