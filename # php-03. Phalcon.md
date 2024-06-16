# php-03. Phalcon
________________________________________
Phalcon Framework  
https://phalcon.io/en-us
________________________________________
## 1. インストール
________________________________________
### 1.1. インストール

Installation - Phalcon Documentation  
https://docs.phalcon.io/latest/installation/

インストールの確認

```bash

# cli側
php -m | grep phalcon

# php-fpm側
# phpinfoを表示させてphalconがヒットすること
```

________________________________________
### 1.2. Webサーバの設定

Webサーバのセットアップ - nginx
https://docs.phalcon.io/latest/webserver-setup/#nginx

注意点

- nginxとphp-fpmが別々の場合、どちらにも同じソースコードをマウントが必要

________________________________________
### 1.3. phalcon devtools

※ コマンドはlinuxのphp cli、composer、unzipが利用できる環境下で行う

```bash
cd /path/to/project
# 安定版ではなく開発版…
composer require phalcon/devtools:5.0.x-dev

# 動作確認
alias phalcon='./vendor/bin/phalcon'
phalcon
```

スケルトンプロジェクト

```bash
phalcon create-project foo-app

# 補足
publicフォルダがドキュメントルートに来るようにする必要がある
そのため、dockerなどでのマウント位置の調整が必要
```

DBテーブルを基にしたCRUD Scaffolding

```bash
phalcon scaffold --table-name zzz_sample
```