# service-01. Slack API
________________________________________
※ 前提

```powershell
# powershellは標準でiwrのエイリアスにcurl指定しているので、そのままだとcurlが使えない（curl自体はWindows10 1809移行に標準で用意されてるのにね！）
del alias:curl
```

________________________________________
## 1. 通常版の要約
________________________________________
### 1.1. Slack App用意&設定～APIを叩いてみるまで

Guided tutorials  
https://api.slack.com/tutorials

chat.postMessage  
https://api.slack.com/methods/chat.postMessage

users.list  
https://api.slack.com/methods/users.list

```powershell
# 1. 以下でAppトークンを用意（Create App）
# https://api.slack.com/apps
#
# 2. OAuth & PermissionsでScopeを追加
# chat:write
# chat:write.customize
# users:read
#
# 3. Basic Information > Install your app > Install to Workspace
# （これは実際には、OAuth認可コード・アクセストークン手続きを開発用にUI化してある）
#
# 4. Slackを開き、Appを対象のchannelに追加
#
# 5. OAuth & Permissions > Bot User OAuth Tokenをどちらかの方法で用いてリクエスト
# トークンは以下の2つのどちらかの方法で指定すること
# A. Authorization: Bearer xoxb-...
# B. Postボディでtoken=xoxb-...
curl -XGET  "https://slack.com/api/auth.test" -H "Authorization: Bearer xoxb-xxxxxxxxxxxx-xxxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxxx"
curl -XPOST "https://slack.com/api/auth.test" -H "Authorization: Bearer xoxb-xxxxxxxxxxxx-xxxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxxx"
curl -XPOST "https://slack.com/api/auth.test" -d "token=xoxb-xxxxxxxxxxxx-xxxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxxx"

# 6. 例
curl -XPOST "https://slack.com/api/chat.postMessage" -d "token=xoxb-xxxxxxxxxxxx-xxxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxxx" -d "channel=CBHAVV36D" -d "text=Hello slack" -d "username=from curl" -d "icon_emoji=whale"
curl -XPOST "https://slack.com/api/users.list" -d "token=xoxb-xxxxxxxxxxxx-xxxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxxx" -d "limit=3"

# 補足 2種類のトークン
# bot(xoxb)  :botユーザによる操作
# user(xoxp) :インストールしたユーザの代理操作
```

________________________________________
### 1.2. イベント処理(reaction_addedの例)

Using the Slack Events API  
https://api.slack.com/apis/connections/events-api

url_verification  
https://api.slack.com/events/url_verification

reaction_added  
https://api.slack.com/events/reaction_added

設定

```text
1. 予めドメイン、サーバ、プログラムを揃え、有効なhttpsのエンドポイントを用意する
    - この段階では、url_verificationが処理できれば良い
2. Slack App > Event Subscriptions > 有効化
3. Request URLにエンドポイントを指定
4. Subscribe to events on behalf of usersにreaction_addedを追加し、Save
5. Slack Appをワークスペースにre-install
```

エンドポイント側の処理

```text
1. リクエストBodyのjsonの内容を見てurl_verificationならchallengeをそのまま応答、それ以外なら続行
2. リクエストが偽物ではないことを検証
    - Slack App > Basic Information > App Credentials > Signing Secretでhash化された値が渡される
    - こちらも同じ手順でhash値を作って比較する
3. あとはやりたいことを自由に書く

※ 詳細はSlack App Samplesのソースを参考の事
```

________________________________________
### 1.3. ワークフロービルダー(有料版機能)の使用例

出来ること

1. 簡単なアンケートなどの用意
2. ステップ実施時に、特定のチャンネルや誰かへDMで通知
3. 結果一覧をCSVでダウンロード

Workflow stepを自作しないと出来ないこと

1. サードパーティとの連携

Workflow stepを自作しても解決できないこと

1. モーダルの自作やワークフロー関連のリソースへのアクセス

________________________________________
### 1.4. Workflow stepの自作

Workflow Builder Steps from Apps  
https://api.slack.com/tutorials/workflow-builder-steps

README.md – steps-from-apps  
https://glitch.com/edit/#!/steps-from-apps?path=README.md%3A1%3A0

bolt-js source  
https://github.com/slackapi/bolt-js/blob/main/src/App.ts

HTTPReceiver.ts handleIncomingEvent  
https://github.com/slackapi/bolt-js/blob/main/src/receivers/HTTPReceiver.ts

設定

```text
1. 1.2と同じ要領で、エンドポイントを用意する（同じプログラム内で処理するなら同じエンドポイントでよい）
2. Slack App > Interactivity & Shortcuts > Enable
3. Request URLにエンドポイントを指定、Save ※ ほとんどの場合、Event Subscriptionsも同じ値を設定する
4. Slack App > Workflow Steps > Enable
5. step追加
6. Slack Appをワークスペースにre-install
7. Slack App > App Home > Enable Home Tab
```

エンドポイント側の処理

```text
※1 bolt-js + node.js on Herokuの場合は、web: node app.jsで起動
※2 APIリクエスト/レスポンスと処理フローははbolt-jsの中身を見たほうが早い

1. イベント処理(reaction_addedの例)の1. と同様
2. イベント処理(reaction_addedの例)の2. と同様
3. 後述の「workflow関連の処理の仕組み」の通りにデータが送られてくるため、それに応じた処理を記述
```

メモ：bolt.jsの要約

```text
利用側：
const app = new App(...);
const ws = new WorkflowStep(...);
app.step(ws);
await app.start(port);

bolt.js側：
App.ts start(...)
  HTTPReceiver.ts start(...)
    httpモジュール http.createServer(...)
    this.requestListener(req, res)
      this.unboundRequestListener.bind(this)
        this.handleIncomingEvent(req, res)
          HTTPModuleFunctions.ts httpFunc.parseHTTPRequestBody(bufferedReq)
            content-type が application/x-www-form-urlencoded なら POSTボディをquerystring.parse
              parse結果のpayloadプロパティがあるならその中身をJSON.parseした内容がbody本体
              parse結果のpayloadプロパティがないならparse結果が本体
            それ以外ならPOSTボディをJSON.parseした内容がbody本体
          body.type が 'url_verification' ならば 適切に返却
          App.ts this.app?.processEvent(event)
            retry判定処理等
            getTypeAndConversation(body)
            this.authorize(source, bodyArg)
              this.authorize = this.initAuthorizeInConstructor(...)
                singleAuthorization(...)
                  runAuthTestForBotToken
                     auth.test APIを呼び出す
            payloadの分類
              event     : body.event がある
              command   : body.command がある
              options   : body.name があるか body.type が 'block_suggestion'
              action    : body.actions があるか body.type が 'dialog_submission' or 'workflow_step_edit'
              shortcut  : body.type が 'shortcut' か 'message_action'
              viewaction: body.type が 'view_submission' か 'view_closed'
            実payloadの取得
              event     : body.event
              viewaction: body.view
              shortcut  : body
              action    : body.actions があるなら actions[0], それ以外ならdefaultにfallback
              default   : body
```

workflow関連の処理の仕組み

```text
workflow_step_edit
ワークフロー作成時にそのステップを「追加・編集を開始する際」に通知される
ワークフロー作成時のポップアップをblock kit方式で用意しviews.openに渡す
詳細はサンプルプログラムを参照の事

workflow_step(rootのtypeはview_submission)
ワークフロー作成時にそのステップの「追加・編集がSaveされる際」に通知される
workflow.updateStepに応答を渡す必要がある

workflow_step_execute
ワークフロー実施時に、そのステップが処理される際に通知される
```

________________________________________
### 1.5. Shortcut & Block Kit Modal

Slack App側

1. Interactivity ON
2. Request URL 設定 ※ ほとんどの場合、Event Subscriptionsも同じ値を設定する
3. ショートカット追加 > グローバル（アタッチ&ショートカットに追加される）

プログラム側

1. shortcutタイプを受け取るので、callback_idでどのショートカットか特定する
    - モーダル処理を開始する
        - https://slack.com/api/views.open
        - title, submitはほぼ定型
        - blocks補足
            - "type": "button"でbuttonアクションを受け取ることが可能
2. モーダルをsubmitした後の処理を受け取るので、callback_idでどのsubmitか特定する
    - view_submission
    - あとは受け取った内容を読み取ってやりたいことをする
    - 正常に終わるなら200応答して終了

________________________________________
## 2. Enterprise Gridエディション対応
________________________________________
Enterprise Gridエディション向けの場合

```text
これらは、通常版と異なる手順が必要
・Enterprise Gridエディション環境の用意
・Enterprise Gridエディション向けSlack Appの設定やOAuth認可コード・アクセストークンの取得方法

1. 開発向けEnterprise Grid用のSlackの用意
Enterprise Grid Sandbox
https://api.slack.com/enterprise/grid/testing



2. Slack Appの用意（用意自体は、通常版とOpt inを除いて同様）
https://api.slack.com/apps

2.1. 通常のCreate App～Scope設定と同様
2.2. Slack App > Org Level Apps > Opt in



3. Enterprise Grid環境へのSlack Appの追加
「Basic Information > Install your app > Install to Workspace」経由の追加ができない
そのため、自前でOAuth認可コード・アクセストークン取得のためのWebサーバを用意して使用が必要
例：
https://.../SlackApiSamples/org-redirect/oauthRedirect.php

3.1. AOuthリクエストを受けとるWebサーバ(要SSL)及び受け取るためのスクリプトを用意する（PHPなど）
3.2. Slack App > AOuth & Permission > Add New Redirect URL に用意したエンドポイントを指定
3.3. Slack AppのClient ID、Client Secletを確認してスクリプトに埋め込む
3.4. 適切な権限を持つSlack ユーザでインストールしたいWorkspaceにログインする
    - 初期設定では「Orgオーナー権限を有り & Workspaceに参加 & Slack Appインストール権限のある」ユーザである必要あり
3.5. 上記の状態で、以下URLアクセスしてリクエスト（これでログイン中の内容に対してSlack App追加依頼AOuthの開始になる）
    - ※ admin:系スコープを含む場合はOrgLevelに対して申請され、含まない場合はWorkspaceに申請される
    - ※ admin:系とその他のリクエストは認可コード・アクセストークンを分ける必要がある(userスコープが必要)ため、場合によってはアプリは2つのトークンを使い分ける必要がある
     https://slack.com/oauth/v2/authorize?scope=...&user_scope=...&client_id=...
    - scope : そのアプリに必要なbotスコープをカンマ区切りで列挙
    - user_scope：そのアプリに必要なユーザスコープをカンマ区切りで列挙
    - client_id：インストールしたいSlack AppのClient ID
3.6. Allow
3.7. Redirect URLに承認された認可コード・アクセストークンを含むリクエストが送られてくる
    - 厳密にはスクリプトは以下のやり取りをする
        1. codeが送られてくる
        2. https://slack.com/api/oauth.v2.access へリクエスト
        3. 認可情報がレスポンスされる



4. 必要に応じてインストールしたユーザのユーザIDも控えて、開発システムで利用する
```
# Enterprise Gridメモ
# OrG向けSCIM APIは必ず「Accept: application/json」「Authorization: Bearer xoxp-...」の2つのヘッダの付与が必要
EnterpriseGridのadmin APIについてもこちらが必要