# Docker Swarm

This document covers the setup and configuration of **Docker Swarm** for a two-node Raspberry Pi cluster.

Docker Swarm enables **multi-node orchestration**, allowing services to be deployed, scheduled, and managed across multiple hosts as a single logical cluster.

---

## Objectives

- Initialize Docker Swarm on the manager node
- Join the worker node to the cluster
- Verify node membership and roles
- Establish the foundation for distributed services

---

## Cluster Context

| Hostname | Role | Responsibility |
| --- | --- | --- |
| `pi-main` | Manager | Cluster control plane |
| `pi-worker` | Worker | Runs scheduled workloads |

Docker Swarm introduces **role separation**, where the manager handles cluster state and scheduling decisions, and workers execute tasks.

---

## Why Docker Swarm

Docker Swarm is used in this cluster to:

- Simulate a real multi-node environment
- Centralize service orchestration
- Automatically place workloads on available nodes
- Prepare for scaling beyond a single host

While more advanced orchestrators exist (e.g., Kubernetes), Docker Swarm provides a simpler and more approachable entry point for learning cluster concepts.

---

## Initialize the Swarm (MANAGER NODE)

The following command is run **only on the manager node (`pi-main`)**.

```bash
docker swarm init --advertise-addr 192.168.50.199
```

Be sure to replace the IP address with the manager node’s actual IP.

Successful output will include:

- Confirmation that the swarm was initialized
- A **join token** for worker nodes

Example output:

```
Swarm initialized: current node (xxxx) is now a manager.

```

![image.png1](attachment:baf0f657-f7f6-407b-85ae-681ef4819cec:image.png)

## Join the Worker Node (WORKER NODE)

On the worker node, type in the command in the picture above

![image.png2](attachment:01f151cf-02ca-4b3e-b2ba-737c5bf60445:image.png)

This command:

- Registers the worker with the manager
- Allows the manager to schedule workloads on the worker
- Establishes secure cluster communication

Terminal should output: “This node joined a swarm as a worker”

## Verify Swarm Status (MANAGER NODE)

Return to **`pi-main`** and verify cluster membership:

```bash
docker node ls

```

Expected output:

```
NAME        STATUS  AVAILABILITY  MANAGER STATUS
pi-main     Ready   Active        Leader
pi-worker   Ready   Active

```

This confirms:

- Both nodes are part of the swarm
- Roles are correctly assigned
- The cluster is operational

![image.png3](attachment:ae19bf6a-3b26-4950-8a75-b8eb816bd11c:image.png)

NOTE: if you have trouble connecting to swarm. Ensure that swarm is active.

![image.png4](attachment:90499b31-2da1-4c6b-8dd8-c5db87545918:image.png)

A simple reboot of the docker can help fix it. 

## Swarm Networking (Overview)

Docker Swarm automatically creates:

- A **control plane** for node communication
- An **ingress network** for service routing
- Support for **overlay networks** across nodes

These features allow containers to communicate securely across hosts without manual network configuration.

Overlay networks and service-level networking are covered in later documentation.

---

## Service Scheduling Behavior

In Swarm mode:

- Services are deployed using `docker service` or stacks
- The manager decides where tasks run
- Workers execute containers assigned to them
- Failed containers can be rescheduled automatically

This behavior closely mirrors production orchestration systems.

## Summary

Docker Swarm transforms the cluster from standalone Docker hosts into a **coordinated multi-node system** by:

- Introducing manager and worker roles
- Enabling centralized scheduling
- Supporting distributed services
- Preparing the environment for scalable workloads

With Swarm enabled, the cluster is ready for deploying multi-node services using Docker stacks.
