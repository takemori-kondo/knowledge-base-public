# service-01. Slack API
________________________________________
Guided tutorials  
https://api.slack.com/tutorials

Using the Slack Events API  
https://api.slack.com/apis/connections/events-api

url_verification  
https://api.slack.com/events/url_verification

reaction_added  
https://api.slack.com/events/reaction_added

________________________________________
## 1. 通常版の要約
________________________________________
### 1.1. Slack App用意&設定～APIを叩いてみるまで

```powershell
# 1. 以下でAppトークンを用意（Create App）
# https://api.slack.com/apps
#
# 2. OAuth & PermissionsでScopeを追加
# （Scopeは、iOS/Androidのcapability宣言と考え方は同じ。例えばbot scopeの以下）
# chat:write
# chat:write.customize
# 
# 3. Basic Information > Install your app > Install to Workspace
# （これは実際には、OAuth認可トークン手続きを開発用にUI化してある）
#
# 3.5. OAuth 許可
#
# 4. Slackを開き、Appを対象のchannelに追加
#
# 5. 準備は整ったのでcurl等で実際にたたいてみる
# OAuth & Permissions のTokenを使用して、例えば以下
curl -XPOST "https://slack.com/api/auth.test?token=<xoxbトークン>"

# 6. chatの例
curl -XPOST -d "token=<xoxbトークン>" -d "channel=C01FY5PVD55" -d "text=Hello slack" -d "username=from curl" -d "icon_emoji=whale" "https://slack.com/api/chat.postMessage"
```

bot(xoxb)トークン/スコープ、user(xoxp)トークン/スコープ

```text
bot(xoxb)トークン/スコープは、botユーザによる操作
user(xoxp)トークン/スコープは、インストールしたユーザアカウントとしての代理操作。EnterpriseGridのadmin APIについてもこちらが必要
```

________________________________________
### 1.2. イベント処理(reaction_addedの例)

Slack Appの設定

```text
1. 以下のEvent Subscriptions で有効化

2. Request URLにイベントをハンドリングするエンドポイントを指定
（この時url_verificationがリクエストされるのでサーバは用意できている必要がある）

3. Subscribe to events on behalf of usersにreaction_addedを追加
```

エンドポイント側に必要な処理

```text
0. 各リクエストは3秒以内に応答できなかった場合、3回まで再送されるため、その仕組みに対応しておくこと
1. url_verification かどうかを見分けて、challengeを応答
2. 
```

________________________________________
## 2. Enterprise Gridエディション対応
________________________________________
Enterprise Gridエディション向けの場合

```text
これらは、通常版と異なる手順が必要
・Enterprise Gridエディション環境の用意
・Enterprise Gridエディション向けSlack Appの設定やOAuth認可トークンの取得方法

1. 開発向けEnterprise Grid用のSlackの用意
Enterprise Grid Sandbox
https://api.slack.com/enterprise/grid/testing



2. Slack Appの用意（用意自体は、通常版とOpt inを除いて同様）
https://api.slack.com/apps

2.1. 通常のCreate App～Scope設定と同様
2.2. Slack App > Org Level Apps > Opt in



3. Enterprise Grid環境へのSlack Appの追加
「Basic Information > Install your app > Install to Workspace」経由の追加ができない
そのため、自前でOAuth認可トークン取得のためのWebサーバを用意して使用が必要

3.1. AOuthリクエストを受けとるWebサーバ(要SSL)及び受け取るためのスクリプトを用意する（PHPなど）
3.2. Slack App > AOuth & Permission > Add New Redirect URL に用意したエンドポイントを指定
3.3. Slack AppのClient ID、Client Secletを確認してスクリプトに埋め込む
3.4. 適切な権限を持つSlack ユーザでインストールしたいWorkspaceにログインする
    - 初期設定では「Orgオーナー権限を有り & Workspaceに参加 & Slack Appインストール権限のある」ユーザである必要あり
3.5. 上記の状態で、以下URLアクセスしてリクエスト（これでログイン中の内容に対してSlack App追加依頼AOuthの開始になる）
    - ※ admin:系スコープを含む場合はOrgLevelに対して申請され、含まない場合はWorkspaceに申請される
    - ※ admin:系とその他のリクエストは認可トークンを分ける必要がある(userスコープが必要)ため、場合によってはアプリは2つのトークンを使い分ける必要がある
     https://slack.com/oauth/v2/authorize?scope=...&user_scope=...&client_id=...
    - scope : そのアプリに必要なbotスコープをカンマ区切りで列挙
    - user_scope：そのアプリに必要なユーザスコープをカンマ区切りで列挙
    - client_id：インストールしたいSlack AppのClient ID
3.6. Allow
3.7. Redirect URLに承認された認可トークンを含むリクエストが送られてくる



4. 必要に応じてインストールしたユーザのユーザIDも控えて、開発システムで利用する
```
