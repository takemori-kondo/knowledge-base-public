# infra-02. Let's Encrypt

最終確認日 2021/03/05

Get Certbot — Certbot 1.11.0.dev0 documentation  
https://certbot.eff.org/docs/install.html#operating-system-packages

________________________________________
## 0. SSL自体の発行の仕組み
________________________________________
1. 証明書発行機関(CA)が次を事前に作成
    - 秘密鍵
    - csr(SSL公開鍵+CA情報)
    - crt
2. 申請者が作成
    - 秘密鍵
    - csr(SSL公開鍵+申請者情報)
3. CAがCA秘密鍵・CAcrt・申請者csrから、最終crt(ルート証明書)と最終cer(中間証明書)が作成される
4. 申請者のApacheに、申請者秘密鍵、最終crt(ルート証明書)、最終cer(中間証明書)を設定する

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