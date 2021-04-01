# middleware-02. Postgres
________________________________________
## 0. How to Install
________________________________________
### 0.1. Install and Settings

Install

```bash
sudo yum -y install https://download.postgresql.org/pub/repos/yum/11/redhat/rhel-7-x86_64/pgdg-redhat11-11-2.noarch.rpm
sudo yum -y install postgresql11-server
export PGSETUP_INITDB_OPTIONS="--encoding=UTF-8 --no-locale"
sudo /usr/pgsql-11/bin/postgresql-11-setup initdb
sudo systemctl enable postgresql-11.service
sudo systemctl start postgresql-11.service
sudo systemctl status postgresql-11.service
sudo passwd postgres
# postgres
su - postgres

# postgres
psql -l
psql
postgres=# ALTER ROLE postgres PASSWORD 'postgres';
postgres=# CREATE ROLE foo CREATEDB LOGIN PASSWORD 'bar';
postgres=# quit
exit

sudo ls /var/lib/pgsql/
sudo vi /var/lib/pgsql/11/data/postgresql.conf
# listen_addresses = '*'
sudo vi /var/lib/pgsql/11/data/pg_hba.conf
# # "local" is for Unix domain socket connections only
# local   all             all                                     md5
# # IPv4 local connections:
# host    all             all             127.0.0.1/32            md5
# host    all             all             192.168.3.0/24          md5
# # IPv6 local connections:
# host    all             all             ::1/128                 md5
# # Allow replication connections from localhost, by a user with the
# # replication privilege.
# local   replication     all                                     md5
# host    replication     all             127.0.0.1/32            md5
# host    replication     all             ::1/128                 md5
sudo systemctl restart postgresql-11.service
sudo systemctl status postgresql-11.service

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