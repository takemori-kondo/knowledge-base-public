# middleware-01ex. LAMP for CentOS Stream8
________________________________________
## 0. How to Install
________________________________________
### 0.1. Install and Settings

Install to CentOS

```bash

# CentOS Stream8
cat /etc/redhat-release
# sudo dnf -y install epel-release
sudo dnf -y reinstall epel-release
sudo dnf -y install https://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo dnf -y install httpd
curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash
sudo dnf -y install mariadb-server
sudo dnf -y install --enablerepo=remi php php-devel php-mbstring php-pdo php-gd php-intl php-xml php-mysqlnd php-openssl php-opcache
sudo systemctl enable httpd.service
sudo systemctl restart httpd.service
sudo systemctl enable mariadb.service
sudo systemctl restart mariadb.service
sudo systemctl enable firewalld.service
sudo systemctl restart firewalld.service
sudo firewall-cmd --permanent --zone=public --add-service=http
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --reload

# unzip, git
sudo dnf -y install unzip
sudo dnf -y install git

# composer
sudo dnf -y install --enablerepo=remi php-json
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```

mysql -u root -p (Default password is empty.)

```sql
select user,host,Password from mysql.user;
grant all privileges on *.* to 'root'@'localhost'  IDENTIFIED BY 'foo' with grant option;
grant all privileges on *.* to 'root'@'127.0.0.1'  IDENTIFIED BY 'foo' with grant option;
grant all privileges on *.* to 'root'@'192.168.%'  IDENTIFIED BY 'foo';
flush privileges;
quit
```

sudo vi /etc/my.cnf

```ini
...
[mysqld]
log-output=FILE

# Disable general log.
general-log=0
general_log_file=""

# Enable slow query log.
slow_query_log=1
slow_query_log_file="/var/log/mysql/slow.log"
long_query_time=0.1
log_queries_not_using_indexes=1

# Enable error log.
log_error="/var/log/mysql/error.log"
log_warnings=1

# Enable binary(replication) log. (MySQL 5.6.5 or later/MariaDB 10.1.6 or later)
# /var/lib/mysql
log_bin=mysql-bin
binlog_format=MIXED
max_binlog_size=134217728
expire_logs_days=7
# server-id=1
# innodb_flush_log_at_trx_commit=1
# sync_binlog=1

# 5.5.3 or later.
character-set-server=utf8mb4
innodb_file_per_table
# 10.2.1 or earlier
# innodb_large_prefix
# innodb_file_format=Barracuda
# innodb_file_format_max=Barracuda
# 10.1.32/10.2.2 or later
innodb_default_row_format=dynamic

[client]
# 5.5.3 or later.
default-character-set=utf8mb4
```

Create & restart

```bash
sudo mkdir /var/log/mysql
sudo touch /var/log/mysql/slow.log
sudo touch /var/log/mysql/error.log
sudo chown mysql.mysql /var/log/mysql
sudo chown mysql.mysql /var/log/mysql/slow.log
sudo chown mysql.mysql /var/log/mysql/error.log
sudo systemctl restart mariadb.service
```

mysql -u root -p

```sql
# character_set_system is utf8
show variables like 'character%';
show global variables like 'character%';

# 10.2.1 or Earlier are "On", "Barracuda"
show variables like 'innodb_large%';
show global variables like 'innodb_large%';
show variables like 'innodb_file%';
show global variables like 'innodb_file%';

# check slow.log
SELECT SLEEP(5);

quit
```

Check logs

```bash
ls /var/log -al
cat /var/log/mysql/slow.log
cat /var/log/mysql/error.log
sudo cat /var/lib/mysql/mysql-bin.000001
```

php test

```bash
vi /var/www/html/phpinfo.php
# <?php phpinfo();

# access by browser
```