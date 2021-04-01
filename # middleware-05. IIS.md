# middleware-05. IIS
________________________________________
## 1. IISの機能有効化

How to Activation

```text
プログラムと機能
  > Windowsの機能の有効化または無効化
    > インターネットインフォメーションサービス
```

Features

IIS                        |def. |Apache相当や備考
---------------------------|-----|-------------------------
HTTP エラー                |V    |略
HTTP リダイレクト          |     |略
WebDAV                     |     |Apacheは認証とセットだが、IISは別々
ディレクトリの参照         |V    |Options +Indexes
既定のドキュメント         |V    |DirectoryIndex index.html
静的なコンテンツ           |V    |略
---                        |     |---
.NET 拡張機能 x.x          |Y    |.NETの有効化
Application Initialization |     |IIS起動時にASP.NETを1度呼び出しておくか
ASP                        |     |略
ASP.NET x.x                |Y    |.NET 拡張機能と項目が分かれているが連動
CGI                        |     |略
ISAPIフィルター            |Y    |mod
ISAPI拡張                  |Y    |mod
WebSocketプロトコル        |     |?
---                        |---  |---
IIS クライアント証明...    |     |?
IP セキュリティ            |     |?
SSL 証明書の集中サポート   |     |?
URL 承認                   |     |?
Windows 認証               |     |?
クライアント証明書...      |     |?
ダイジェスト認証           |     |略
BASIC認証                  |     |略
要求のフィルタリング       |V    |略
---                        |     |---
静的なコンテンツの圧縮     |V    |略
動的なコンテンツの圧縮     |     |略
---                        |     |---
HTTP ログ                  |V    |/var/log/httpd/access_log
ODBC ログ                  |     |?
カスタム ログ              |     |?
トレース                   |     |?
ログツール                 |     |?
要求の監視                 |     |?

※ IISの最低限のWebサーバ機能に加え、ASP.NETを有効化した状態

URL Rewrite

```text
IISにはApacheでは当たり前のmod_rewrite機能が標準付属しないので別途インストール
```

________________________________________
## 2. IISとApache

デフォルトフォルダや用語対応関係

IIS                              |Apache
---------------------------------|-------------------------
svchost.exe for w3svc            |httpd
C:\inetpub\wwwroot               |/var/www/html
C:\inetpub\logs\LogFiles\W3SVC1\ |/var/log/httpd/
IISマネージャ                    |/etc/httpd/conf/httpd.conf
IISマネージャ > 新規サイト       |VirtualHost > DocumentRoot
IISマネージャ（個々の機能）      |/etc/httpd/conf.d/*
ISAPI                            |mod
IISのアーキテクチャ              |不明。おそらくworker相当
svchost.exe for w3svc プロセス   |制御用プロセス
アプリケーションプール           |なし。Apacheはサーバ分けてリバースプロキシ
アプリケーションプール > ワーカー|子プロセス