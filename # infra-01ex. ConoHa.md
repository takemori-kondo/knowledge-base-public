# infra-01ex. ConoHa
________________________________________
## 1. VM作成
________________________________________
典型例 （Ver3。リージョン指定がそもそもない）

- VPS
    - Ubuntu 24.04 LTS
    - GitLab
    - 12カ月 ※ 操作が不安な場合、一旦時間課金で作ってから変更がよい
    - 2GB
    - root パスワード
    - ネームタグ
    - 自動バックアップ なし
    - 追加ストレージ なし
    - セキュリティグループ カスタム（SSHとWebを許可したものを用意する）
    - SSH key 新規作成
    - スタートアップスクリプト なし

GitLabの設定

- SSHでログイン
- sudo nano /etc/gitlab/gitlab.rb
    - prometheus['enable'] = false
    - alertmanager['enable'] = false
    - mattermost['enable'] = false
    - mailroom['enable'] = false
    - registry['enable'] = false
    - pages['enable'] = false
- gitlab-ctl reconfigure
- gitlab-rails console -e production
    - rubyコンソールが起動する
    - user = User.find_by_username('root')
    - user.password = 'NewSecurePassword123!'
    - user.password_confirmation = 'NewSecurePassword123!'
    - user.save!
    - exit
- gitlab-ctl restart

旧 典型例（Ver2）

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

