# eco-07. Git Server
________________________________________
## 1. CUIの場合
________________________________________
オプション（gitユーザ作成等）

```bash
adduser git
su - git
cd ~
mkdir .ssh
# 以下、ssh接続の追加など
```

サーバ構築

```bash
# 本来必要なのはgitのみ
# 2つを入れるとinitの内容自体が変わり、Gitプロトコル対応の初期化になる
sudo yum -y install git git-all git-daemon
sudo mkdir /var/lib/git
sudo mkdir /var/lib/git/foo.git
cd /var/lib/git/foo.git
sudo git init --bare --shared=true
cd /var/lib/git
sudo chmod -R a+w foo.git

# クライアント側
# sshキーペア作成（既にサーバに対してペアがあれば省略しても良い）
#     SourceTree > SSH Agent
#     PuTTY Key Generator > Generate
#     Save public key username.pub
#     Save private key username.ppk
# サーバに公開鍵をアップロードし、authorized_keysの末尾にコピペ
cat ~/.ssh/username.pub >> ~/.ssh/authorized_keys
# ssh://vagrant@192.168.3.18:22/var/lib/git/foo.git
# vagrant@192.168.3.18:/var/lib/git/foo.git
```

認証なしダウンロードのみ（Gitプロトコル、push不可）

```bash
sudo yum -y install git git-all git-daemon

# CenOS6.xの場合
# --base-pathが機能していない。/var/lib/git
#         disable         = no
sudo vi /etc/xinetd.d/git
sudo /etc/init.d/xinetd start

# CentOS7.xの場合
# 設定変更不要 /usr/lib/systemd/system/git@.service
sudo systemctl enable git.socket
sudo systemctl start git.socket

sudo mkdir /var/lib/git/bar.git
cd /var/lib/git/bar.git
sudo git init --bare --shared=true

# 必要に応じて、9418ポートを開放

# git://192.168.3.18/bar.git
```

________________________________________
## 2. GUIの場合
________________________________________
### 2.1. 全般

```text
クラウド・ホスティング
GitHub    :GitHub社
gitlab.com:GitLab社
Bitbucket :Atlassian社

OSS
Gitlab CC :MIT。GitLab社。
GitBucket :Apache License v2。takezoeさんによるGitHubクローン
```

________________________________________
### 2.2. GitBucket

```bash
# GitBucket4.13は、Java8
sudo yum -y install java-1.8.0-openjdk java-1.8.0-openjdk-devel-1.8.0-devel
# CentOS6.xは7.0.77がデフォルト。epel必要。LAN用なので、Tomcat単体でHttpサーバさせてOK
# CentOS7.xは7.0.XXがデフォルト。epel必要。LAN用なので、Tomcat単体でHttpサーバさせてOK
sudo yum -y install epel-release
sudo yum -y install tomcat tomcat-webapps
/usr/sbin/tomcat version
# sudo systemctl enable tomcat.service
sudo chkconfig tomcat on
# sudo systemctl start tomcat.service
sudo service tomcat start
# GitBucketインストール
sudo yum -y install wget
sudo wget -P /usr/share/tomcat/webapps/ https://github.com/takezoe/gitbucket/releases/download/4.13/gitbucket.war

# http://192.168.3.18:8080/
# http://192.168.3.18:8080/gitbucket/
```

________________________________________
### 2.3. Gitlab

Install

```text
etc.
```

Initial settings (gitlb-ctl)

```bash
vi /etc/gitlab/gitlab.rb
# Edit external_url.
gitlab-ctl reconfigure
reboot now
```

Other Settings

```text
1. Access by browser.
2. Reset password. (for gitlab's root user)
3. Create normal user.
4. Logout & login & reset password.
5. Create ssh key on your pc. (ssh-keygen -t rsa)
6. User's Setting > SSH Keys > Add ssh key.
```

Use

```text
1. New project on gitlab.
2. Add remote repository for your pc's local repository.(*)
3. Push

* for sourcetree
if putty, add ssh key for sourcetree's option.
if openssh, set .ssh/config
```

権限メモ

```text
公開レベル
https://docs.gitlab.com/ee/public_access/public_access.html

public   : ログインしなくても見れる
internal : サインインすれば見れる（external user除く）
private  : メンバーのみが見れる。なお、private ProjectにはGuestを追加できない

ユーザー/グループレベル
https://docs.gitlab.com/ee/user/permissions.html#project-members-permissions

Guest    : 基本的な閲覧やソースのClone・ダウンロード、Issueの追加
Developer: Guest + push、mergeなど。プロジェクト管理などはできない
Owner    : 何でもできる 
```
