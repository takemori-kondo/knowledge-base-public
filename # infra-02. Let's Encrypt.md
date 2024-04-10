# infra-02. Let's Encrypt
________________________________________
最終確認日 2021/03/05

Get Certbot — Certbot 1.11.0.dev0 documentation  
https://certbot.eff.org/docs/install.html#operating-system-packages
________________________________________
## 0. SSL自体の仕組み
________________________________________
## 0.1. 公開鍵暗号とRSA

要約

```text
1. 公開鍵は暗号化できる（AをA'に出来る）
2. 秘密鍵は復号できる  （A'をAに出来る）
3. 公開鍵とA'がわかっても、秘密鍵やAの特定が困難。また公開鍵とAとA'がわかっても、秘密鍵の特定が困難
4. 公開鍵から秘密鍵は作れない
5. （RSAの場合）秘密鍵から公開鍵を作れる
6. （RSAの場合）あるデータBに公開鍵と秘密鍵を逆の順番で適用可能で、かつ元のデータBが得られる(※)

※補足：RSA以外のデジタル署名の方式では必ずしも双方の操作がy=f(x)の形式ではなく
y,z=f(x), x=g(y,z)という操作のペアで引数と戻り値の型が異なり、そもそも入れ替えられないものがある
また、そもそも公開鍵暗号技術と署名検証に必要な操作要件は等価ではなく、たまたま流用できたのでRSAは流用されただけに過ぎない
```

2つの使い方

```text
暗号化　 : 元データを公開鍵で暗号化 => 秘密鍵で暗号を復号
署名検証 : 元データを秘密鍵で復号   => 公開鍵で署名を暗号化

どちらの場合も、元データを得られる
ただし、公開鍵暗号の実質的な標準であるRSAは、「秘密鍵から公開鍵を作れる」という特性がある
このため、公開鍵と秘密鍵を逆に使用して秘密鍵を公開鍵のように使ってしまうとセキュリティにならない
```

補足

```text
SSHの仕組みは、公開鍵をサーバに登録し、ログイン認証時は秘密鍵で復号してリクエストする
SSLの仕組みは、秘密鍵をサーバに登録し、
    1. クライアントによる署名検証時には上位証明書に付属する公開鍵を利用して署名検証
    2. 署名検証完了後、共通鍵交換する際にはサーバ証明書の公開鍵で暗号化して送信
```

________________________________________
### 0.2. ブラウザとクライアント間の通信

SSLって何？意味や仕組みをわかりやすく解説！  
https://ssl.sakura.ad.jp/column/ssl/


```text
Browser                                        Web Server
   |  SSLしたいからお前の身分証みせろ！            |
   |---------------------------------------------->|
   |                                               |
   |  俺の身分証はこれだ                           |
   |<----------------------------------------------|
   |                                               |
 確認中                                            |
   |                                               |
   |                                               |
   |OK、合言葉作って送るね                         |
   |---------------------------------------------->|
   |                                               |
   |OK受け取った。通信開始してくれ                 |
   |<----------------------------------------------|
   |                                               |

※1 身分証 = 証明書ファイル群(crt、cer)
※2 確認中 = 署名検証を繰り返してルート証明書を辿れるか
※3 合言葉 = そのセッションで有効な共通鍵。これはsession keyとも呼ばれるが、http session idとは別
```

________________________________________
### 0.3. 証明書の仕組み

トラストチェーン

```text
1. ルート認証局は、「俺正しいから」と自分で作った証明書を作って各PCに渡している。Windows Updateなどで刷新される
    - 発行者 : ルート認証局
    - 主体   : ルート認証局
    - 公開鍵 : ルート認証局の秘密鍵に対する公開鍵
    - 署名   : ルート認証局による署名
2. 中間CAは、上位の認証局に署名してもらった中間CA自身の証明書（中間証明書）を持つ
    - 発行者 : 上位の認証局
    - 主体   : 中間CA
    - 公開鍵 : 中間CAの秘密鍵に対する公開鍵
    - 署名   : 上位の認証局による署名
3. あなたのサーバは、上位認証局に証明してもらったサーバ証明書を（加えて、↑の中間証明書も）持つ
    - 発行者 : 上位の認証局
    - 主体   : あなた
    - 公開鍵 : あなたの秘密鍵に対する公開鍵
    - 署名   : 上位の認証局による署名
```

ニュアンス

```text
- 発行者 : 文字列
- 主体   : 文字列
- 公開鍵 : 公開鍵
- 署名   : csrを元に作ったハッシュ値をさらに署名者の秘密鍵で復号した値
```

CAによる有料の証明書を発行してもらう場合の手続き

```text
1. 申請者が以下を用意
    - 申請者の秘密鍵
    - csr（秘密鍵に対する公開鍵と、主体情報を1つのファイルにしたもの）
2. csrをCAに渡す
3. CAは、csrを元にcrt（サーバ証明書）を作成する（openssl x509 -req相当操作）
4. 申請者はcrt（サーバ証明書）とcer（中間証明書）を受け取る
5. 申請者はサーバに、申請者の秘密鍵、crt、cerを設定する
    - 中間証明書が2つの場合もある（CA側で1つのファイルにくっつけてくれない。不親切）
        - ただのテキストファイルなので、2つのファイルの文字列を両方記載すればよい
```

________________________________________
## 1. Certbot
________________________________________
### 1.1. Install via Certbot

Certbot sslizes the named web site by let's encrypt.

/etc/httpd/conf/httpd.conf

```pache
NameVirtualHost *:80
<VirtualHost *:80>
    ServerAdmin email.account@foo.com
    DocumentRoot /var/www/html
    ServerName bar.com
</VirtualHost>
```

Install mod_ssl & restart httpd

```bash
sudo dnf install -y mod_ssl
sudo systemctl restart httpd.service
```

Download & run certbot

```bash
sudo yum -y install epel-release
sudo yum -y install python-certbot-apache
sudo certbot --apache

# Enter email address
#     email.account@foo.com
# Please read the Terms of Service at…
#     Y
# Would you be willing…
#     Y
# (No names were found in your configuration files. Please …)
#     bar.com
# Which names would you like to activate HTTPS for?
# 1: bar.com
#     1
# Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
# -------------------------------------------------------------------------------
# 1: No redirect - Make no further changes to the webserver configuration.
# 2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
# new sites, or if you're confident your site works on HTTPS. You can undo this
# change by editing your web server's configuration.
#     2

sudo systemctl restart httpd.service
sudo certbot renew --dry-run
```

________________________________________
### 1.2. Update via Certbot

```bash
sudo certbot renew --webroot-path /var/www/html/
sudo systemctl reload httpd
```