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
apt update && apt upgrade -y
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

#### Connect without paddword. Type locally
```bash
ssh-agent ~/.ssh/key_name
# If you got an error "Could not open a connection to your authentication agent", make next
eval `ssh-agent -s` # and repeat the command
```
Now you can create alias to connection
```bash
nano ~/.bash_profile # .bashrc in WSL
# add next line to the bottom of file
alias ssh-conn='ssh <user_name>@<server-ip>'
```

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

## Install Nginx
```bash
# if you have preinstalled Apatche2 remove it
sudo service apache2 stop
sudo apt-get purge apache2 apache2-utils apache2.2-bin apache2-common
sudo apt-get autoremove --purge
whereis apache2
sudo rm -Rf /etc/apache2 /usr/lib/apache2 /usr/include/apache2
```

```bash
sudo apt install ufw nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx
sudo -i
for i in ssh http https
do
           ufw allow $i
done
sudo ufw enable
sudo ufw status
```

## Installing MySQL to Manage Site Data

#### Install MySQL by typing
```bash
sudo apt update
sudo apt-get install mariadb-server mariadb-client -y
sudo mysql_secure_installation
# Set password
sudo mysql -u root -p
MariaDB [(none)]> GRANT ALL ON *.* TO 'admin'@'localhost' IDENTIFIED BY 'admin' WITH GRANT OPTION;
MariaDB [(none)]> FLUSH PRIVILEGES;
MariaDB [(none)]> exit;
```

## Install PHP
```bash
sudo apt install software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt update
sudo apt -y install php7.4
sudo apt install php7.4-fpm php7.4-common php7.4-mysql php7.4-xml php7.4-xmlrpc php7.4-curl php7.4-gd php7.4-imagick php7.4-cli php7.4-dev php7.4-imap php7.4-mbstring php7.4-opcache php7.4-soap php7.4-zip php7.4-intl -y
sudo apt install unzip
cd ~
curl -sS https://getcomposer.org/installer -o composer-setup.php
# Copy verified hash on https://composer.github.io/pubkeys.html and paste it to $HASH variable
HASH=<hash_from_https://composer.github.io/pubkeys.html>
php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
composer --version
sudo nano /etc/php/7.4/fpm/php.ini
```
```ini
file_uploads = On
allow_url_fopen = On
short_open_tag = On
memory_limit = 256M
cgi.fix_pathinfo = 0
upload_max_filesize = 100M
max_execution_time = 360
date.timezone = Europe/Kiev
```

## Install Node.js
```
cd ~
curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh
sudo bash nodesource_setup.sh
sudo apt install -y nodejs
```

## Install the let’s encrypt Certbot Client
```bash
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx -y
sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
sudo certbot --nginx -d example.com -d www.example.com
```
#### Cron job for let's encrypt renewal.
```bash
sudo nano /etc/crontab
43 6 * * * certbot renew --post-hook "systemctl restart nginx"
```