# linux-01. CentOS
________________________________________
## 1. Operation
________________________________________
### 1.1. bash

bash operations

```text
Tab
Ctrl+C
Ctrl+R
... | ...
... | grep -i ...
... | xargs ...
```

Common commands

```bash
# ifconfig
ip a
cd ~
pwd
ls -al
find ./ -name *.jpg -type f 2>/dev/null -ls
find ./ -name *.jpg -type d 2>/dev/null -ls
find ./ -mtime -3 -type f 2>/dev/null -ls
find ./ -mtime -3 -type d 2>/dev/null -ls
mkdir foo
mkdir -p foo
cp from to
cp -r from to
mv from to
rm foo
rm -dfr foo

sudo ...
su - user
exit
yum -y install ...
mount /dev/cdrom /mnt
shutdown -h now
shutdown -r now

ssh-keygen -t rsa
printenv PATH
export ...=...

chown user foo
chgrp user foo
chmod 777 foo
chmod -R 744 foo // グループはreadonly
chmod -R 754 foo // グループはread-executable
chmod -R 774 foo // グループはall OK
chmod a+w foo
chmod -R a+w foo

# Permission of selinux
sudo chcon -R -t etc_t /etc/letsencrypt
sudo semanage fcontext -a -t etc_t "/etc/letsencrypt(/.*)?"
sudo restorecon -R -v /etc/letsencrypt
# apacheから外部へのソケット通信を許可する(mod/fcgi問わずphpのcurl_exeなど含む)
sudo setsebool -P httpd_can_network_connect on

ps aux --sort -pcpu | head -6
ps aux --sort -rss | head -6
df -h
top -d2
# -H : -
# -S : -
# -a : all
# -c        0  : -
# -d ulimited  : max data area.
# -f unlimited : max file size for writing by shell. 
# -l    64(KB) : max lockable memory.
# -m unlimited : max memory.
# -n      1024 : max descriptor.
# -p         8 : max process for user.
# -s  8192(KB) : max stack area.
# -t unlimited : max CPU time.
ulimit -n

# system total max discriptor count
# 191741
cat /proc/sys/fs/file-max

vi /etc/DIR_COLORS
vi /etc/passwd
```

Run network tui

```bash
# netconfig
## system-config-network

nmtui
systemctl restart NetworkManager
```

Service commands

```bash
# CentOS6, AmazonLinux
# service --status-all
# service sshd status
# service sshd start
# service sshd stop
# service sshd restart
# chkconfig --list
# chkconfig --list sshd
# chkconfig sshd on
# chkconfig sshd off

# CentOS7
systemctl list-units --type=service
systemctl status sshd.service
systemctl start sshd.service
systemctl stop sshd.service
systemctl restart sshd.service
systemctl list-unit-files --type=service
systemctl is-enabled sshd.service
systemctl enable sshd.service
systemctl disable sshd.service
```

________________________________________
### 1.2. vi

```text
:q
:q!
:wq
i
/
n
N
ESC
```

________________________________________
## 2. Distribution etc.
________________________________________
Amazon Linux AMI 2015～

```text
OS         : ver.7 kernel + ver.5/ver.6 userland
SELinux    : disabled
iptables   : disabled (* Firewall support by AWS-SG)
yum repo   : amzn-main、amzn-updates
/opt/aws   : AWS tools
```

Yum Repositories & Org

```bash
# Redhat
#   RHEL
  
# Fedora project (Sponsored by Redhat)
#   Fedora
#   Epel
sudo yum install epel-release

# CentOS project
#   CentOS
#   https://www.centos.org/download/

# Rackspace
#   OpenStack

# IUS project (Sponsored by Rackspace)
#   IUS(CentOS6/Amazon linux AMI)
#   https://centos6.iuscommunity.org/ius-release.rpm
# IUS(CentOS7)
#   https://centos7.iuscommunity.org/ius-release.rpm

# Remi's RPM repository
#   remi
```

Linux Distribution Tree

```text
Debian
  Ubuntu
  Raspbian
Redhat
  RHEL
  CentOS
  Oracle Linux
  Amazon Linux
Slackware
```
