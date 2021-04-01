# middleware-01. LAMP
________________________________________
## 0. How to Install
________________________________________
### 0.1. Install and Settings
Prepare VM

```text
VirtualBox etc.
```

Install to CentOS

```bash

## if CentOS6
## CentOS6 default yum = Apache2.2.15, MySQL5.1, PHP5.3
# cat /etc/redhat-release
# sudo yum -y install epel-release
# sudo yum -y install https://rpms.remirepo.net/enterprise/remi-release-6.rpm
# sudo yum -y install httpd
# sudo rpm -Uvh http://dev.mysql.com/get/mysql57-community-release-el6-7.noarch.rpm
# sudo yum install mysql-community-server
# sudo yum -y install --enablerepo=remi-php72 php php-devel php-mbstring php-pdo php-gd php-intl php-xml php-mysqlnd php-pecl-mcrypt php-opcache
# sudo chkconfig httpd on
# sudo service httpd restart
# sudo chkconfig mysqld on
# sudo service mysqld restart
# sudo chkconfig iptables on
# sudo service iptables restart
# sudo vi /etc/sysconfig/iptables
## -A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
## -A INPUT -m state --state NEW -m tcp -p tcp --dport 443 -j ACCEPT
# sudo service iptables restart

# if CentOS7
# CentOS7 default yum = Apache2.4.x,  MariaDB5.5.56 or 10.5.x, PHP5.4
cat /etc/redhat-release
sudo yum -y install epel-release
sudo yum -y install https://rpms.remirepo.net/enterprise/remi-release-7.rpm
sudo yum -y install httpd
curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash
sudo yum -y install mariadb-server
sudo yum -y install --enablerepo=remi-php72 php php-devel php-mbstring php-pdo php-gd php-intl php-xml php-mysqlnd php-pecl-mcrypt php-opcache
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
sudo yum -y install unzip
sudo yum -y install git

# composer
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
# sudo service mysqld restart
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

________________________________________
### 0.2. Debug Environment

Install

```bash
sudo yum -y install --enablerepo=remi-php72 php-pecl-xdebug
```

sudo vi /etc/php.d/15-xdebug.ini

```ini
...
[xdebug]
xdebug.default_enable = 1
xdebug.idekey = "vscode"
xdebug.remote_enable = 1
xdebug.remote_port=9000
xdebug.remote_autostart = 1
xdebug.remote_host=<develop pc's IP>
```

```bash
# sudo service httpd restart
sudo systemctl restart httpd.service
```

________________________________________
## 1. Apache (httpd)
________________________________________
### 1.1. Paths

Settings

```text
/etc/httpd/conf/httpd.conf
/etc/httpd/conf.d/...
/etc/httpd/conf.module.d/...
# If php is installed, php.conf exists.
/etc/httpd/conf.d/php.conf
```

Logs

```text
/var/log/httpd/access_log
/var/log/httpd/error_log
/var/log/httpd/ssl_access_log
/var/log/httpd/ssl_error_log
```

________________________________________
### 1.2. FAQ

Default Parameters

```apache
# CentOS's default is prefork & mod_php.
# Event mpm   is good at pages.
# Prefork mpm is good at api.
Timeout 60
KeepAlive On
MaxKeepAliveRequests 100
KeepAliveTimeout 5
StartServers 5
ServerLimit 256
# MaxClients 256
MaxRequestWorkers 256
# MaxRequestsPerChild 0
MaxConnectionsPerChild 0
MinSpareServers 5
MaxSpareServers 10
```

Frequent commands

```bash
cat /etc/httpd/conf/httpd.conf | grep DocumentRoot
cat /etc/httpd/conf/httpd.conf | grep -A6 \<Directory
cat /etc/httpd/conf.modules.d/00-mpm.conf | grep LoadModule
apachectl -h
apachectl -V
apachectl -v
apachectl -t
apachectl -k graceful
```
Enable mod_status

```apache
ExtendedStatus On
<Location /server-status>
    SetHandler server-status
    Require ip 127.0.0.1 <your grobal ip>
</Location>
<Location /server-info>
    SetHandler server-info
    Require ip 127.0.0.1 <your grobal ip>
</Location>
```

________________________________________
### 1.3. Basic, Digest

Basic or Digest

```bash
# sudo mkdir /var/lib/dav
# sudo chown apache:apache /var/lib/dav
# if basic
# sudo htpasswd -c /etc/httpd/conf/htpasswd <username>
sudo htdigest -c /etc/httpd/conf/htdigest "<AuthName>" <username>
sudo vi /etc/httpd/conf.d/dav.conf
```

```apache
<IfModule mod_dav_fs.c>
    DAVLockDB /var/lib/dav/lockdb
</IfModule>

<Directory "/var/www/html">
    DAV on
#    AuthType     Basic
    AuthType     Digest
    AuthName     "WebDAV Server"
#    AuthUserFile /etc/httpd/conf/htpasswd
    AuthUserFile /etc/httpd/conf/htdigest
    AuthGroupFile /dev/null
    Require valid-user
</Directory>
```

________________________________________
### 1.4. Auth Samples

All ok

```apache
Require all granted
```

Any auth (localhost or ip or host)


```apache
Require ip 127.0.0.1 <specific ip>
Require host <specific domain>
```

Any auth (LAN)

```apache
Require ip 127.0.0.1
Require ip 10
Require ip 172.16 172.17 172.18 172.19
Require ip 172.20 172.21 172.22 172.23
Require ip 172.24 172.25 172.26 172.27
Require ip 172.28 172.29 172.30 172.31
Require ip 192.168
```

All auth (reject specific ip)

```apache
<RequireAll>
    Require all granted
    Require not ip 111.111.111.111
</RequireAll>
```

________________________________________
## 2. MariaDB (mysql-server, mysqld)
________________________________________

Basic knowledge

```text
Oracle        MySQL/MariaDB
data file   : ibdata, .ibf, .frm
redo buffer : innodb log buffer
redo log    : ib_logfile
achive log  : bin log(It is also used for replication.)
```

Settings

```text
/etc/my.cnf
/etc/my.cnf.d/...
/etc/mysql/my.cnf
~/my.cnf
```

Dump

```bash
mysql -u root -p
mysqldump -u root -p foo_db > bar.dump
mysqld_safe --skip-grant-tables

* Default or localhost do not use tcp/ip, but 127.0.0.1 uses tcp/ip.
```

Add access privileges

```sql
select user,host,Password from mysql.user;
grant all privileges on *.* to <user>@<remoteIP>  IDENTIFIED BY "<password>";
flush privileges;
```

________________________________________
## 3. PHP
________________________________________
Settings

```text
/etc/php.ini
/etc/php.d/...
/etc/php-zts.d/...
```

Useful

```bash
php -i | grep xdebug
```

phpinfo.php

```php
<?php
ini_set( 'display_errors', 1 );
echo "サーバのIPアドレス:[{$_SERVER["SERVER_ADDR"]}]<br>";
session_start();
if (isset($_SESSION['session_id_16']) && !empty($_SESSION['session_id_16'])) {
    echo "Loaded session! : ";
}
else {
    echo "New session! : ";
}
$_SESSION['session_id_16'] = substr(session_id(), 0, 16);
echo $_SESSION['session_id_16'];
phpinfo();
```
