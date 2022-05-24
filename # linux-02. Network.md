# linux-02. Network
________________________________________
## 0. 要約
________________________________________
ネットワーク関連の設定ファイル

|ファイル                          |package    |役割
|----------------------------------|-----------|--------------------------------------------------------------------------------------
|/etc/services                     |setup(※1) |serviceとportのメモ(※2)。dnf/yum install時のport競合避け、netstatの表示などに使われる
|/etc/protocols                    |setup      |手動で変更は想定しない
|/etc/rpc                          |glibc      |手動で変更は想定しない
|/etc/nsswitch.conf                |glibc2     |手動で変更は想定しない。名前解決の優先順位(リゾルバ)自体の設定
|/etc/host.conf                    |glibc      |手動で変更は想定しない。名前解決の優先順位(リゾルバ)自体の設定
|/etc/resolv.conf                  |-          |リゾルバ(一時)設定。network.serviceなどにリセットされるため実質一時ファイル
|/etc/hosts                        |setup      |hostsファイル(※3)
|/etc/networks                     |initscripts|hostsのネットワークアドレス版。使用するプログラムは稀
|/etc/sysconfig/network            |-          |ネットワーク設定ファイル(共通)。よく編集する機会があるのはこれ
|/etc/sysconfig/network-scripts/xxx|-          |ネットワーク設定ファイル(個別)。よく編集する機会があるのはこれ
|/etc/hostname                     |-          |CentOS7より。このPCのホスト名のみが書かれた設定ファイル(※4)

(※1) linuxにはsetupという名のpackageが存在する。詳細を知りたい場合は、ggラビリティがひどいのでsetup.noarchで検索するとよい  
(※2) 行儀がいいserviceなら起動時や変更時に最新の状態でファイルを更新する。行儀が悪いと実使用ポートが変わってもこのファイルが変わらない  
(※3) 実際のところ、/etc/hostsのループバックアドレスにホスト名を記載しないとうまく動かないプログラムは少なくない
(※4) /etc/sysconfig/networkなどに書く方式は旧方式になった(複数NICがそれぞれ別hostname持つ場合は依然として旧方式だが。)

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

※ RHEL系のCentOS7以降は、nmtuiプログラム経由での変更が推奨されている

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


