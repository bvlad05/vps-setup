# VPS setup

## Connect to server
```bash
ssh root@<server-ip>
```
Type a password

## Change root password
```bash
passwd
```
Type a new password

## Update os
```bash
apt-get update
apt-get upgrade
```

## Add a new user
```bash
adduser <username>
```
Type a new password, other info may be skipped

## Add user to sudo group
```bash
adduser <username> sudo
```

## Disallow connections by root
```bash
nano /etc/ssh/sshd_config
```
`ctrl+w` and type `PermitRootLogin` and set it `no` value. Then save it `ctrl+o` and exit `ctrl+x`

## Restart SSHD
```bash
systemctl restart ssh
```
And logout at **LAST TIME**
```bash
exit
```

## Login with a new user
```bash
ssh <username>@<server-ip>
```
Type a password

## Copy public ssh key
Copy the entry of id_rsa.pub
```bash
mkdir ~/.ssh
nano ~/.ssh/authorized_keys # and paste pub key
sudo chmod -R 700 ~/.ssh
```
Then save it `ctrl+o` and exit `ctrl+x`
