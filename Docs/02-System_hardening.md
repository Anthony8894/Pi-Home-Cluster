# System Hardening (Baseline)

This document covers the **baseline hardening steps** I apply to each Raspberry Pi in this cluster running **Ubuntu Server LTS**.

The goal here is simple: lock things down early, reduce the attack surface, and set up a secure foundation **before any services are installed**. Even though this is a home lab, the hardening steps I use are based on real-world Linux server best practices and are applicable to small servers, edge devices, and production-adjacent environments.

## Cluster-Wide Hardening Policy

All hardening steps in this document are applied **consistently across every node** in the cluster, including both manager and worker nodes.

Each Raspberry Pi runs its own instance of Ubuntu Server, so each system is hardened individually to ensure the cluster remains secure as a whole.

---

## Hardening Objectives

- Limit unauthorized access
- Reduce the number of exposed services
- Enforce least-privilege access
- Improve logging and visibility
- Keep the system stable while making security improvements

Keep in mind that these are **practical baseline hardening**, not full compliance frameworks (such as CIS Level 2 or STIG).



## 1. System Updates & Patch Management

Before making any security changes, the system should be fully up to date.

```bash
sudo apt update && sudo apt upgrade -y
```

(image)

Keeping packages updated ensures that known vulnerabilities are patched before services are deployed.


