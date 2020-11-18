# Website configuration

Also you can use conf [generator](https://lemp-confs-generator.vercel.app/) 

## Add user for the project
```sh
useradd -d /home/{{ username }} -g www-data -m -s /bin/bash {{ username }}
usermod -a -G www-data {{ username }}
usermod -a -G sudo {{ username }}
passwd {{ username }}
su {{ username }}
```

## PHP-Fpm configuration
```sh
cd /etc/php/7.4/fpm/pool.d/
sudo nano {{ site_name }}.conf

# And add next lines of code
[{{ site_domain }}]
listen = /run/php/php-{{ site_name }}.sock
listen.mode = 0666
user = {{ username }}
group = www-data

php_admin_value[upload_tmp_dir] = /home/{{ username }}/tmp
php_admin_value[date.timezone] = Europe/Kiev
php_admin_value[open_basedir] = /home/{{ username }}
php_admin_value[post_max_size] = 512M
php_admin_value[upload_max_filesize] = 512M
php_admin_value[cgi.fix_pathinfo] = 0
php_admin_value[short_open_tag] = On
php_admin_value[memory_limit] = 512M
php_admin_value[session.gc_probability] = 1
php_admin_value[session.gc_divisor] = 100
php_admin_value[session.gc_maxlifetime] = 28800;
php_admin_value[error_log] = /home/{{ username }}/logs/php_errors.log;

pm = dynamic
pm.max_children = 10
pm.start_servers = 2
pm.min_spare_servers = 2
pm.max_spare_servers = 4
```

## Nginx configuration
```sh
cd /etc/nginx/sites-available
sudo nano 01-{{ site_domain }}.conf
# And add next lines of code

server {
  listen [::]:80;
  listen 80;
  server_name {{ site_domain }} www.{{ site_domain }};

  access_log /home/{{ username }}/logs/nginx_access.log;
  error_log /home/{{ username }}/logs/nginx_error.log;

  client_max_body_size 100M;

  gzip on;
  gzip_min_length 1000;
  gzip_proxied any;
  gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript application/javascript;
  gzip_disable "MSIE [1-6]\.(?!.*SV1)";
  gzip_comp_level 6;


  root /home/{{ username }}/{{ site_domain }}/www;
  index index.php;

  location ~* ^.+\.(ogg|ogv|svg|svgz|eot|otf|woff|mp4|ttf|rss|atom|jpg|jpeg|gif|png|ico|zip|tgz|gz|rar|bz2|doc|xls|exe|ppt|tar|mid|midi|wav|bmp|rtf)$ {
    access_log off;
    log_not_found off;
    expires max;
  }

  location / {
    rewrite ^/.*$ /index.php last;
  }

  location ~ ^/index.php {
    include snippets/fastcgi-php.conf;
    fastcgi_pass unix:/var/run/php/php-{{ site_name }}.sock;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
  }

  # Whitelist
  ## Let October handle if static file not exists
  location ~ ^/favicon\.ico { try_files $uri /index.php; }
  location ~ ^/sitemap\.xml { try_files $uri /index.php; }
  location ~ ^/robots\.txt { try_files $uri /index.php; }
  location ~ ^/humans\.txt { try_files $uri /index.php; }
  ## Let nginx return 404 if static file not exists
  location ~ ^/storage/app/uploads/public { try_files $uri 404; }
  location ~ ^/storage/app/media { try_files $uri 404; }
  location ~ ^/storage/temp/public { try_files $uri 404; }
  location ~ ^/modules/.*/assets { try_files $uri 404; }
  location ~ ^/modules/.*/resources { try_files $uri 404; }
  location ~ ^/modules/.*/behaviors/.*/assets { try_files $uri 404; }
  location ~ ^/modules/.*/behaviors/.*/resources { try_files $uri 404; }
  location ~ ^/modules/.*/widgets/.*/assets { try_files $uri 404; }
  location ~ ^/modules/.*/widgets/.*/resources { try_files $uri 404; }
  location ~ ^/modules/.*/formwidgets/.*/assets { try_files $uri 404; }
  location ~ ^/modules/.*/formwidgets/.*/resources { try_files $uri 404; }
  location ~ ^/modules/.*/reportwidgets/.*/assets { try_files $uri 404; }
  location ~ ^/modules/.*/reportwidgets/.*/resources { try_files $uri 404; }
  location ~ ^/plugins/.*/.*/assets { try_files $uri 404; }
  location ~ ^/plugins/.*/.*/resources { try_files $uri 404; }
  location ~ ^/plugins/.*/.*/behaviors/.*/assets { try_files $uri 404; }
  location ~ ^/plugins/.*/.*/behaviors/.*/resources { try_files $uri 404; }
  location ~ ^/plugins/.*/.*/reportwidgets/.*/assets { try_files $uri 404; }
  location ~ ^/plugins/.*/.*/reportwidgets/.*/resources { try_files $uri 404; }
  location ~ ^/plugins/.*/.*/formwidgets/.*/assets { try_files $uri 404; }
  location ~ ^/plugins/.*/.*/formwidgets/.*/resources { try_files $uri 404; }
  location ~ ^/plugins/.*/.*/widgets/.*/assets { try_files $uri 404; }
  location ~ ^/plugins/.*/.*/widgets/.*/resources { try_files $uri 404; }
  location ~ ^/themes/.*/assets { try_files $uri 404; }
  location ~ ^/themes/.*/resources { try_files $uri 404; }
}

# Create symbolic link
cd ../sites-enabled
sudo ln -s ../sites-available/01-{{ site_domain }}.conf
```

## Create database + user
```sh
mysql -u root -p
CREATE DATABASE IF NOT EXISTS {{ db_name }} CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
CREATE USER '{{ db_username }}'@'localhost' IDENTIFIED BY '{{ db_password }}';
GRANT USAGE ON *.* TO '{{ db_username }}'@'localhost' IDENTIFIED BY '{{ db_password }}' REQUIRE NONE WITH MAX_QUERIES_PER_HOUR 0 MAX_CONNECTIONS_PER_HOUR 0 MAX_UPDATES_PER_HOUR 0 MAX_USER_CONNECTIONS 0;
GRANT ALL PRIVILEGES ON `{{ db_name }}`.* TO '{{ db_username }}'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;
```

## Finish
```sh
sudo -u {{ username }} mkdir /home/{{ username }}/tmp
sudo -u {{ username }} mkdir /home/{{ username }}/logs
sudo -u {{ username }} mkdir /home/{{ username }}/{{ site_domain }}
sudo -u {{ username }} mkdir /home/{{ username }}/{{ site_domain }}/www
sudo -u {{ username }} mkdir /home/{{ username }}/.ssh
sudo -u {{ username }} chmod 755 /home/{{ username }}/.ssh
sudo -u {{ username }} ssh-keygen -t rsa -C "{{ email }}"
```

## Add site to hosts
```sh
sudo nano /etc/hosts
# Add next line
127.0.0.1 {{ site_domain }}

# Restart services
sudo nginx -t
sudo service php7.4-fpm restart
sudo service nginx restart
sudo service mysql restart

# SSL
sudo certbot --nginx -d {{ site_domain }} -d {{ site_domain }}
```
