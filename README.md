# VPS setup

## Base setup

#### Connect to server
```bash
ssh root@<server-ip>
```
Type a password

#### Change root password
```bash
passwd
```
Type a new password

#### Update os
```bash
apt-get update
apt-get upgrade
```

#### Add a new user
```bash
adduser <username>
```
Type a new password, other info may be skipped

#### Add user to sudo group
```bash
adduser <username> sudo
```

#### Disallow connections by root
```bash
nano /etc/ssh/sshd_config
```
`ctrl+w` and type `PermitRootLogin` and set it `no` value. Then save it `ctrl+o` and exit `ctrl+x`

#### Restart SSHD
```bash
systemctl restart sshd
```
And logout at **LAST TIME**
```bash
exit
```

#### Login with a new user
```bash
ssh <username>@<server-ip>
```
Type a password

#### Copy public ssh key
Copy the entry of id_rsa.pub
```bash
mkdir ~/.ssh
nano ~/.ssh/authorized_keys # and paste pub key
sudo chmod -R 700 ~/.ssh
```
Then save it `ctrl+o` and exit `ctrl+x`

#### Disable connections by password
```bash
sudo nano /etc/ssh/sshd_config
```
`ctrl+w` and type `PasswordAuthentication` and set it `no` value. Then save it `ctrl+o` and exit `ctrl+x`

```bash
sudo systemctl restart sshd
sudo visudo
```
`ctrl+w` and type `%sudo` and set it `ALL=(ALL:ALL) NOPASSWD:ALL` value.(Only you will have access to this server) Then save it `ctrl+o` and exit `ctrl+x`

And logout and try again
```bash
exit
```

## Firewall setup
```bash
sudo apt install ufw
sudo ufw allow 'Nginx HTTP'
sudo ufw enable
sudo ufw status
```
**NOW NGINX MUST WORK!**

## Installing MySQL to Manage Site Data

#### Install MySQL by typing
```bash
sudo apt install mysql-server
sudo mysql_secure_installation
```
