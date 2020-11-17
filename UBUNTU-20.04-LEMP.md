# Ubuntu 20.04 LEMP
Provider: fornex.com

**After install add your SSH key**

## Connect to your server
```sh
# Change root password
passwd
# Update OS
apt update && apt upgrade -y && apt autoremove -y
```

## Install Nginx + PHP + MariaDB + Node.js + yarn + Certbot
```sh
cd ~ && curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh && bash nodesource_setup.sh

apt install -y nginx php-common php-cli php-curl php-json php-gd php-mysql php-xml php-zip php-fpm php-mbstring php-bcmath php-pear php-xmlrpc php-gd php-imagick php-intl php-dev php-imap  php-opcache php-soap mariadb-server-10.3 unzip certbot python3-certbot-nginx yarn nodejs

service php7.4-fpm start
service nginx start
service mysql start
nginx -t
```

## Firewall
```sh
ufw enable && ufw allow http && ufw allow https && ufw allow ssh && ufw reload && ufw status
sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
```

## Install Composer
```sh
cd ~ && curl -sS https://getcomposer.org/installer -o composer-setup.php
# Copy verified hash on https://composer.github.io/pubkeys.html and paste it to $HASH variable
HASH=756890a4488ce9024fc62c56153228907f1545c228516cbf63f885e036d37e9a59d27d63f46af1d4d07ee0f76181c7d3
php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php --install-dir=/usr/local/bin --filename=composer --version=1.9.3
```

## Configurate mysql root user
```sh
mysql -u root
> USE mysql;
> UPDATE user SET plugin = 'mysql_native_password' WHERE User = 'root';
> ALTER USER 'root'@'localhost' IDENTIFIED BY '1234567890';
> FLUSH PRIVILEGES;
> EXIT;
```

## Base Nginx configuration
```sh
# Create default conf
cd /etc/nginx/sites-available && rm default && nano 00-default.conf

# And paste it into conf file
server {
    listen [::]:80;
    listen      80;
    server_name "";
    return      444;
}

# Create symbolic link
cd ../sites-enabled && rm default && ln -s ../sites-available/00-default.conf
```

## Base php-fpm configuration
```sh
cd /etc/php/7.4/fpm/pool.d/ && rm www.conf
```

## Cron job for let's encrypt renewal
```sh
sudo nano /etc/crontab
43 6 * * * certbot renew --post-hook "systemctl restart nginx"
```