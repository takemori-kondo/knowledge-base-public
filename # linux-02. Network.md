# linux-02. Network
________________________________________
## 1. BOOTPROTO, Gateway, DNS
________________________________________
/etc/sysconfig/network

```ini
NETWORKING=yes
GATEWAY=x.x.x.x
DNS1=x.x.x.x
DNS2=x.x.x.x
```

/etc/sysconfig/network-scripts/ifcfg-foo

```ini
DEVICE=eth0
BOOTPROTO=dhcp/none(static)
IPADDR=x.x.x.x
DEFROUTE=yes/no
HOSTNAME=foo
TYPE=ethernet
ONBOOT=yes
```

________________________________________
## 2. iptables vs firewalld
________________________________________
/etc/sysconfig/iptables

```ini
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 443 -j ACCEPT
```

firewalld（デフォルトポートの場合）

```bash
firewall-cmd --list-services --zone=public  --permanent
firewall-cmd --permanent --zone=public --add-service=http
firewall-cmd --reload
```

________________________________________
## 3. hosts
________________________________________
### 3.1 hosts (customized name resolving rule.)

/etc/hosts

________________________________________
### 3.2. name resolving order

/etc/hosts.conf or /etc/nsswitch.conf

```text
* /etc/resolv.confは一時データ
```


