# Networking & Connectivity

This document outlines the **networking configuration** used for the Raspberry Pi cluster, including hostnames, IP addressing, and how nodes are accessed and identified on the network.

The goal of this phase is to ensure **reliable connectivity**, **clear node identification**, and **secure remote access** before deploying containerized services.

---

## Networking Objectives

- Ensure each node is easily identifiable on the network
- Provide stable and predictable IP addressing
- Enable secure remote administration
- Verify inter-node connectivity
- Minimize future networking changes

---

## Cluster Nodes

The cluster currently consists of two Raspberry Pi nodes:

| Hostname | Role | Operating System |
|--------|-----|------------------|
| `pi-main` | Manager | Ubuntu Server LTS |
| `pi-worker` | Worker | Ubuntu Server LTS |

Each node runs independently and must be reachable over the network.

---

## IP Addressing Strategy

The cluster is deployed on a private home network using the `192.168.50.0/24` subnet.

To ensure consistent access and prevent IP changes after reboot, each Raspberry Pi is assigned a **static IP address** (via router reservation or local configuration).

Example:

| Hostname | IP Address |
|--------|------------|
| `pi-main` | `192.168.50.199` |
| `pi-worker` | `192.168.50.200` |

This approach ensures:
- Predictable SSH access
- Stable service endpoints
- Easier troubleshooting

---

## Hostname Configuration

Each Raspberry Pi is assigned a unique and descriptive hostname.

Verify hostname:

```bash
hostname
```

If required, hostnames can be modified using:

sudo hostnamectl set-hostname <hostname>

Distinct hostnames make it easier to identify nodes during administration and log review.
Secure Remote Access (SSH)

All nodes are managed remotely using SSH.

Standard SSH access pattern:

ssh <username>@<ip-address>

Example:

ssh piborgergade@192.168.50.199

SSH access is:

    Key-based

    Restricted to non-root users

    Protected by a host firewall

These controls were established during the system hardening phase.
Inter-Node Connectivity

Basic connectivity between nodes is verified using standard network tools.

Test connectivity from one node to another:

ping pi-worker

Or by IP address:

ping 192.168.50.200

Successful responses confirm:

    Network reachability

    Proper routing

    No firewall blocks between nodes

Reliable inter-node communication is required for cluster orchestration and monitoring.
Network Discovery & Troubleshooting

To identify devices on the network:

    Router management interface

    ARP table

    Direct console access (if required)

Useful commands:

ip a
ip route

These commands help validate interface configuration and default gateway settings.
Notes on Scalability

This networking setup is intentionally simple and stable.

As the cluster grows, future improvements may include:

    DNS-based name resolution

    VLAN segmentation

    Dedicated management networks

    Cloud or VPN-based remote access

These enhancements are outside the scope of the initial setup but are compatible with the current design.
Summary

This networking configuration provides a stable and secure foundation by:

    Using predictable IP addressing

    Assigning clear hostnames

    Enabling secure SSH access

    Verifying inter-node communication

With networking in place, the cluster is ready for container orchestration and service deployment.
