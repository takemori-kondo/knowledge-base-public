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
## 2. Sample
________________________________________
以下のサンプルを参照してください

https://github.com/takemori-kondo/ansibleSamples.git

ansibleLamp

- main.yml
- hosts/dev.txt
- templates/selinux_config
- templates/my.cnf