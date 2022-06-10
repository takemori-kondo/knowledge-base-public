# vm-03ex. Ansible from centos to ubuntu
________________________________________
## 0. 補足
________________________________________
Apacheに関して

- 正常にインストールされた場合、/etc/www/html下にapache2のデフォルトページが作られる
- ただし、既にindex.htmlがいる時などの場合、作られない

mariadbに関して

- ansibleでのパスワード変更時にno logしてないので、警告が出る
- dbを用意しないので、ログインしてdbを作る必要あり
- 外部アクセスを許可するには、bind-address、ユーザgrandの両方が必要
-- bind-addressは、テンプレートより後に読み込まれるファイルで指定されるので書き換え必要

ユーザgrant対応

```sql
grant all privileges on *.* to 'root'@'192.168.%'  IDENTIFIED BY 'foo';
flush privileges;
```

bind-address対応

```bash
# sudo vi /etc/mysql/mariadb.cnf
[mysqld]
bind-address=0.0.0.0
# sudo systemctl restart mariadb.service
```

________________________________________
## 1. Ansible Directory
________________________________________
Ansible directory

```text
AnsibleRoot
    hosts
    private_keys
        private_key_xxx
    templates
    main.yml
```

________________________________________
## 2. Settings
________________________________________
hosts/dev.txt

```ini
[dev]
192.168.3.253:22

[dev:vars]
ansible_python_interpreter=/usr/bin/python3
```

main.yml

```yaml
# Ansible server
# sudo yum -y install epel-release
# sudo yum -y install ansible
# sudo yum -y install openssh-clients
## cd /home/vagrant/vagrant_shared/ansible
## cp private_keys/private_key ../../
## sudo chmod 0600 ../../private_key
## ansible-playbook -i hosts/dev.txt main.yml -u vagrant --private-key="../../private_key"
# key: key=val key=valはAnsibleの独自拡張。複雑だとちゃんと動かない
---
- name: install middlewares
  hosts: dev
  tasks:
  
#Ubuntuは初期状態でSELinuxはinstallされていない
#- name: disable selinux
#    become: yes
#    shell: bash setenforce 0
#    ignore_errors: true
#  - name: set selinux/config
#    become: yes
#    template:
#      src: templates/selinux_config
#      dest: /etc/selinux/config
#      owner: root
#      group: root
#      mode: 644

  - name: update apt list
    become: yes
    apt:
      update_cache: yes

  - name: install apache2
    become: yes
    apt: name=apache2 state=latest
  - name: start apache2
    become: yes
    service: name=apache2 state=started enabled=yes
    ignore_errors: true
  
  - name: install mariadb
    become: yes
    apt: name=mariadb-server state=latest
  - name: start mariadb
    become: yes
    service: name=mariadb state=started enabled=yes
    ignore_errors: true
  - name: set my.cnf
    become: yes
    template:
      src: templates/my.cnf
      dest: /etc/my.cnf
      owner: root
      group: root
      mode: 644
  - name: touch log files (/var/log/mysql/)
    become: yes
    file: path=/var/log/mysql           state=directory owner=mysql group=mysql mode=0755
  - name: touch log files (/var/log/mysql/slow.log)
    become: yes
    file: path=/var/log/mysql/slow.log  state=touch     owner=mysql group=mysql mode=0755
  - name: touch log files (/var/log/mysql/error.log)
    become: yes
    file: path=/var/log/mysql/error.log state=touch     owner=mysql group=mysql mode=0755
  
  - name: setup mariadb grant(install python3-pymysql)
    become: yes
    apt: name=python3-pymysql state=latest
  - name: setup mariadb grant(root grant1 root@localhost)
    become: yes
    mysql_user:
      login_user: root
      login_unix_socket: /var/run/mysqld/mysqld.sock
      name: 'root'
      host: 'localhost'
      password: 'foo'
      priv: '*.*:ALL,GRANT'
      state: present
      check_implicit_admin: yes
    ignore_errors: true
  - name: restart mariadb
    become: yes
    service: name=mariadb state=restarted
    ignore_errors: true
  
  - name: install software-properties-common
    become: yes
    apt:     name=software-properties-common  state=latest
  - name: apt_repository ondrej/php
    become: yes
    apt_repository:
      validate_certs: no
      repo: 'ppa:ondrej/php'
      state: present
#ondrej/phpのphpには、ctype, fileinfo, json, openssl, pdo, tokenizerが含まれている
#laravel8, cakephpに必要なもの + php-cli, php-dev, php-gd, php-opcache
  - name: install php8.1
    become: yes
    apt:     name=php8.1             state=latest
  - name: install php8.1-bcmath
    become: yes
    apt:     name=php8.1-bcmath      state=latest
  - name: install php8.1-cli
    become: yes
    apt:     name=php8.1-cli         state=latest
  - name: install php8.1-dev
    become: yes
    apt:     name=php8.1-dev         state=latest
  - name: install php8.1-gd
    become: yes
    apt:     name=php8.1-gd          state=latest
  - name: install php8.1-intl
    become: yes
    apt:     name=php8.1-intl        state=latest
  - name: install php8.1-mbstring
    become: yes
    apt:     name=php8.1-mbstring    state=latest
  - name: install php8.1-mysql
    become: yes
    apt:     name=php8.1-mysql       state=latest
  - name: install php8.1-opcache
    become: yes
    apt:     name=php8.1-opcache     state=latest
  - name: install php8.1-xml
    become: yes
    apt:     name=php8.1-xml         state=latest
  - name: restart apache2
    become: yes
    service: name=apache2 state=restarted enabled=yes
    ignore_errors: true
  
  - name: install unzip
    become: yes
    apt:     name=unzip state=latest
  - name: install git
    become: yes
    apt:     name=git   state=latest

  - name: create directory (/tmp/composer)
    become: yes
    file: path=/tmp/composer state=directory mode=0777  
  - name: get composer to /tmp/composer
    get_url: url=https://getcomposer.org/installer dest=/tmp/composer/installer
  - name: install composer
    become: yes
    shell: chdir=/tmp/composer php installer
  - name: move composer
    become: yes
    shell: mv /tmp/composer/composer.phar /usr/local/bin/composer
```

templates/selinux_config

```ini
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of three two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

templates/my.cnf

```ini
#
# This group is read both both by the client and the server
# use it for options that affect everything
#
[client-server]

#
# This group is read by the server
#
[mysqld]
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

#
# include all files from the config directory
#
# It doesn't exist in ubuntu file directory
#!includedir /etc/my.cnf.d

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
