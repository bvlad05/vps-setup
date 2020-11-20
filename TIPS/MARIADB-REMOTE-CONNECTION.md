# Allow remote connection (MariaDB)
```sh
nano /etc/mysql/mariadb.conf.d/50-server.cnf
```
Find `bind-address` and replace its value with `0.0.0.0`
```sh
systemctl restart mariadb.service
apt install net-tools
netstat -anp | grep 3306
```
And you should find the result that looks like the one below
```sh
tcp    0    0 0.0.0.0:3306    0.0.0.0:*    LISTEN    3213/mysqld
```

### Access from Remote Clients
```sh
mysql -u root -p
```
Create user
```sql
CREATE USER 'remote_user'@'%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON *.* TO 'remote_user'@'%';
FLUSH PRIVILEGES;
EXIT;
```

### If your Ubuntu server has firewall enabled, then you will want to open connection to the database server.

```sh
ufw allow 3306/tcp
ufw reload
```

### Test it
```sh
mysql -u remoter_user -h <server IP address or hostname> -p
```

## MySQL GUI Tools
* [HeidiSQL](https://www.heidisql.com/) (Windows)
* [Sequel Pro](https://www.sequelpro.com/) (Mac)
* [DBeaver](https://dbeaver.io/) (Windows, Mac, Linux)