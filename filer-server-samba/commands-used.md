## commands used for samba
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