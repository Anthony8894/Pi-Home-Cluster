## commands used for samba (installation)
all commands used in ubuntu terminal. Top to bottom
[samba download/install guide](https://ubuntu.com/tutorials/install-and-configure-samba#3-setting-up-samba)

## install samba
sudo apt update
sudo apt install samba

## version check
smbd --version

## make directory
mkdir /etc/piborgergade/sambashare/

## edit samba config
once the directory is created, access the samba config file
sudo nano /etc/samba/smb.conf

## restart service
sudo systemctl restart smbd

## add samba user
sudo smbpasswd -a piborgergade



### commands used for acess-control

mkdir ~/sambashare/hr 
mkdir ~/sambashare/it

* what is does: creates directories
* why: these are the folders being shared over the network

## list directory details

ls -ld ~/sambashare/hr

* What it does: Shows permissions, owner, and group
* Why: Used to verify access control settings

## user management (create linux users, before samba user)

sudo adduser hr_leader
sudo adduser it_leader

* What it does: Creates system users
* Why: Users must exist before being added to Samba

## add samba user

sudo smbpasswd -a hr_leader
sudo smbpasswd -a it_leader

* What it does: Adds users to Samba authentication system
* Why: Required for network login to file shares

## List Samba users

sudo pdbedit -L

* What it does: Displays Samba user database
* Why: Confirms users are added correctly

## 👥 Group Management (create groups)

sudo groupadd hr
sudo groupadd it

* What it does: Creates user groups
* Why: Used for role-based access control

## Add users to groups

sudo usermod -aG hr hr_leader
sudo usermod -aG it it_leader

* What it does: Adds users to groups
* Why: Grants users access based on role

## Verify group membership

groups hr_leader
groups it_leader

* What it does: Shows which groups a user belongs to
* Why: Confirms correct access assignment

## 🔐 Permissions & Ownership (Change group ownership)

sudo chown :hr ~/sambashare/hr
sudo chown :it ~/sambashare/it

* What it does: Assigns folder to a group
* Why: Ensures only correct group has access

## Set permissions

sudo chmod 770 ~/sambashare/hr
sudo chmod 770 ~/sambashare/it

* What it does: Sets read/write/execute permissions
* Why: Restricts access to owner and group only

## Fix parent directory access

sudo chmod 771 /home/piborgergade

* What it does: Allows traversal into home directory
* Why: Required for Samba users to reach shared folders

## ⚙️ Service Management (Restart Samba)

sudo systemctl restart smbd

* What it does: Restarts Samba service
* Why: Applies configuration changes

## Check service status

sudo systemctl status smbd

* What it does: Shows if Samba is running
* Why: Used for troubleshooting

💻 Windows Commands (Client Side, Clear network connections)

net use * /delete

* What it does: Removes cached network sessions
* Why: Prevents credential conflicts