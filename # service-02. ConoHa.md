# service-02. ConoHa
________________________________________
## 1. VM作成
________________________________________
典型例

- リージョン : 東京
- サービス : VPS
    - VPS割引切符 : 利用しない
    - サイズ : 512MB
    - イメージタイプ : CentOS 7.9 64bit
    - root パスワード
    - ネームタグ
    - オプション
        - 追加ディスク : 使用しない
        - 接続先ポート IPv4 : SSH, Web
        - 接続先ポート IPv6 : SSH, Web
        - SSH Key : キーを新規作成, 自動生成, 追加 -> キーを選択に変わる
        - スタートアップスクリプト : 使用しない

Tera TermでのSSH接続ショートカット例

D:\Dropbox\Documents\EtCetera\Software\teraterm-4.100\ttermpro.exe <ip>:22 /auth=publickey /user=root /keyfile="<生成された秘密鍵pem>"

