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
`CTRL+W` and type `PermitRootLogin` and set it `no` value
