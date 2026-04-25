# 🔐 Access Control Implementation (Samba)

## Overview
Expanded the initial Samba file server to support multiple users and role-based access control. This simulates a real-world environment where different departments have restricted access to specific shared folders.

---

## Users Created
- hr_leader → HR access
- it_leader → IT access

---

## Groups Created
- hr
- it

---

## Folder Structure
/home/piborgergade/sambashare/
├── hr/
└── it/

---

## Access Rules

| User       | HR Folder | IT Folder |
|------------|----------|----------|
| hr_leader  | ✅        | ❌        |
| it_leader  | ❌        | ✅        |

---

## Key Configuration

### Folder Ownership
Assigned group ownership:

- hr folder → hr group
- it folder → it group

### Permissions
Set permissions to:

chmod 770


Meaning:
- owner → full access
- group → full access
- others → no access

---

### Samba Configuration

[hr]
path = /home/piborgergade/sambashare/hr
read only = no
browsable = yes
valid users = @hr
force group = hr

[it]
path = /home/piborgergade/sambashare/it
read only = no
browsable = yes
valid users = @it
force group = it


---

## Issues Encountered

### Issue: Access Denied (even with correct setup)

#### Cause
Users could not traverse the parent directory:
/home/piborgergade


#### Fix

chmod 771 /home/piborgergade



This allowed users to access subdirectories without exposing full directory contents.

---

### Issue: Windows Credential Conflicts

#### Cause
Windows cached previous login sessions

#### Fix
net use * /delete


---

## What I Learned

- Linux permissions apply to ALL parent directories, not just the target folder
- Samba authentication is separate from Linux authentication
- Group-based access control is more scalable than per-user permissions
- Windows caches credentials, which can cause misleading access errors

---

## Next Steps
- Add more users and departments
- Implement shared/common folders
- Integrate with DNS (Pi-hole)
- Explore centralized authentication (LDAP/AD)