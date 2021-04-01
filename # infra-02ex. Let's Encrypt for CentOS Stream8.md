# infra-02ex. Let's Encrypt for CentOS Stream8

最終確認日 2021/03/05

Certbot - Centosrhel8 Haproxy  
https://certbot.eff.org/lets-encrypt/centosrhel8-haproxy

________________________________________
## 1. Certbot
________________________________________
### 1.1. Install via Certbot

Certbot sslizes the named web site by let's encrypt.

/etc/httpd/conf/httpd.conf

```apache
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
sudo dnf -y reinstall epel-release
sudo dnf -y install snapd
sudo systemctl enable --now snapd.socket
sudo ln -s /var/lib/snapd/snap /snap

# system reboot

sudo snap install core; sudo snap refresh core
sudo dnf remove certbot
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
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