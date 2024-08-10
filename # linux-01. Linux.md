# linux-01. Linux
________________________________________
## 1. 基本操作とコマンド
________________________________________
### 1.1. bash（CentOS/Ubuntu共通）

bash補助操作とパイプ

```text
Tab
Ctrl+C
Ctrl+R
... | ...
... | grep -i ...
... | xargs ...
sudo ...
```

履歴操作

```bash
cp /dev/null ~/.bash_history
unset HISTFILE
```

基本コマンド

```bash
ip a
ping 8.8.8.8
cd ~
pwd
ls -al
find / -name *.log
mkdir -p foo
cp -r from to
mv from to
rm -dfr foo

exit
sudo mount /dev/cdrom /mnt
ssh-keygen -t rsa
printenv PATH
export ...=...

chown user foo
chgrp user foo
chmod -R 744 foo // グループはreadonly
chmod -R 754 foo // グループはread-executable
chmod -R 774 foo // グループはall OK
```

Permission of selinux

```bash
sudo chcon -R -t etc_t /etc/letsencrypt
sudo semanage fcontext -a -t etc_t "/etc/letsencrypt(/.*)?"
sudo restorecon -R -v /etc/letsencrypt
# apacheから外部へのソケット通信を許可する(mod/fcgi問わずphpのcurl_exeなど含む)
sudo setsebool -P httpd_can_network_connect on
```

システム、ディスク、ディスクリプタ状況

```bash
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
cat /proc/sys/fs/file-max
```

デーモン管理

```bash
# CentOS7, Ubuntu18
sudo systemctl list-unit-files --type=service
sudo systemctl status sshd
sudo systemctl start sshd
sudo systemctl stop sshd
sudo systemctl restart sshd
sudo systemctl is-enabled sshd.service
sudo systemctl enable sshd.service
sudo systemctl disable sshd.service
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
