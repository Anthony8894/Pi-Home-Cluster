# 📁 Samba File Server (Homelab)

## Overview
I set up a Samba file server on my Raspberry Pi to simulate how shared drives work in a real company environment. This is something I see a lot at my MSP job (users accessing shared folders, permission issues, etc.), so I wanted to actually understand how it works behind the scenes.

---

## What I Wanted to Learn
- How file sharing works over the network (SMB)
- How Linux handles users and permissions
- How access control works in a real environment
- Troubleshooting “can’t access shared drive” type issues

---

## Setup (High-Level)

### Installed Samba
Used apt to install Samba and verified the service was running.

### Created a Shared Folder
Created a folder at: /srv/samba/company


### Users & Groups
- Created a group to manage access
- Added users to that group
- This makes it easier to control permissions instead of doing everything per user

### Permissions
- Set ownership of the folder to the group
- Adjusted permissions so only allowed users can access it

### Samba Config
- Edited the config file to create a share called `company`
- Linked it to the folder path
- Enabled authentication

### Restarted Service
Restarted Samba to apply everything

---

## Testing
From my Windows machine, I connected using: (under development)
