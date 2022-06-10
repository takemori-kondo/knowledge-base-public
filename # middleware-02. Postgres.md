# middleware-02. Postgres
________________________________________
## 0. How to Install
________________________________________
### 0.1. Install and Settings

Linux downloads (Red Hat family)  
https://www.postgresql.org/download/linux/redhat/

Install

```bash
sudo yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
sudo yum install -y postgresql14-server
export PGSETUP_INITDB_OPTIONS="--encoding=UTF-8 --no-locale"
sudo /usr/pgsql-14/bin/postgresql-14-setup initdb
sudo systemctl enable postgresql-14
sudo systemctl start postgresql-14
sudo systemctl status postgresql-14
sudo passwd postgres
# postgres
su - postgres
```
login psql

```bash
# postgres
psql -l
psql
```

postgres=#

```text
ALTER ROLE postgres PASSWORD 'postgres';
CREATE ROLE foo CREATEDB LOGIN PASSWORD 'bar';
quit
```

Other settings & confirmation

```bash
## end su - postgres
# exit

sudo ls /var/lib/pgsql/
sudo vi /var/lib/pgsql/14/data/postgresql.conf
# listen_addresses = '*'
sudo vi /var/lib/pgsql/14/data/pg_hba.conf
# # "local" is for Unix domain socket connections only
# local   all             all                                     scram-sha-256
# # IPv4 local connections:
# host    all             all             127.0.0.1/32            scram-sha-256
# host    all             all             192.168.3.0/24          scram-sha-256
# # IPv6 local connections:
# host    all             all             ::1/128                 scram-sha-256
# # Allow replication connections from localhost, by a user with the
# # replication privilege.
# local   replication     all                                     scram-sha-256
# host    replication     all             127.0.0.1/32            scram-sha-256
# host    replication     all             ::1/128                 scram-sha-256
sudo systemctl restart postgresql-14.service
sudo systemctl status postgresql-14.service

psql -U postgres -d postgres
# postgres
# quit
psql -U foo -d postgres
# bar
# quit
createdb -U foo test
# bar
psql -l -U foo
# bar
psql -U foo -d test
# bar
# create table accounts(id bigint  primary key, name varchar(255));
# insert into accounts values(1, 'Aclice');
# insert into accounts values(2, 'Bob');
# select * from accounts;
# quit
```