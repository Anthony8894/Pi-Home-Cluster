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



## System Updates & Patch Management

Before making any security changes, the system should be fully up to date.

```bash
sudo apt update && sudo apt upgrade -y
```

![image1](/Screenshots/System_Hardening/01-System_hardening.png)

Keeping packages updated ensures that known vulnerabilities are patched before services are deployed.


## Enable Automatic Security Updates

To reduce manual maintenance and ensure critical security patches are applied regularly, automatic security updates are enabled using unattended-upgrades.
```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure unattended-upgrades
```

This configuration allows the system to automatically install security updates, typically running once per day by default.

![image2](/Screenshots/System_Hardening/02-System_hardening.png)

## User & Privilege Management
Disable Root SSH Access

Direct root SSH login significantly increases risk and is unnecessary for day-to-day administration.

Edit the SSH configuration file:
```bash
sudo nano /etc/ssh/sshd_config
```
![image3](/Screenshots/System_Hardening/03-System_hardening.png)

Make sure that this is present by removing the "#": 
PermitRootLogin no

![image4](/Screenshots/System_Hardening/04-System_hardening.png)

Save the file:
CTRL + O -------> Enter
CTRL + X 

Restart SSH to apply the change:
```bash
sudo systemctl restart ssh
```

After this change, all administrative tasks are performed using a non-root user with sudo privileges.

## Verify sudo access

Confirm that the administrative user can successfully elevate privileges:
```bash
sudo whoami
```

The expected output should be root.

![image5](/Screenshots/System_Hardening/05-System_hardening.png)


## SSH hardening
SSH is the primary management interface for this system and should be secured accordingly.

Disable Password Authentication (Key-Based Access)

Password-based SSH authentication is vulnerable to brute-force attacks. SSH key authentication provides stronger security.

Edit the SSH configuration file:
```bash
sudo nano /etc/ssh/sshd_config
```
Set the following values: 
```bash
PasswordAuthentication no
PubkeyAuthentication yes
```

![image6](/Screenshots/System_Hardening/06-System_hardening.png)
![image6](/Screenshots/System_Hardening/07-System_hardening.png)

Save the file and restart SSH
```bash
sudo systemctl restart ssh
```

NOTE: Always verify that key-based SSH access works before disabling password authentication to avoid locking yourself out.


## Firewall configuration (UFW)
A host-based firewall is used to restrict inbound network traffic. Can be boring but super important.

Configure and Enable UFW

Set default firewall policies:
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Make sure to allow SSH access:
```bash
sudo ufw allow ssh
```
Enable firewall:
```bash
sudo ufw enable
```
Verify firewall status:
```bash
sudo ufw status verbose
```

Expected behavior:

Only explicitly allowed ports are accessible

All other inbound traffic is denied by default

![image8](/Screenshots/System_Hardening/08-System_hardening.png)


##  Disable Unnecessary Services

To further reduce the attack surface, verify that only required services are running.

List active services:

```bash
systemctl list-units --type=service --state=running
```

This helps ensure:

Fewer exposed processes

Lower resource usage

Reduced potential vulnerabilities

## Logging & Audit Readiness

As boring as it sounds, logging is essential for troubleshooting and security monitoring.

Verify System Logging

Ubuntu Server uses systemd-journald by default.

View recent system logs: 
```bash
journalctl -xe
```

View authentication-related logs:
```bash
sudo cat /var/log/auth.log
```

These logs will later be forwarded to wazuh for centralized monitoring, alerting, and security analysis. 
