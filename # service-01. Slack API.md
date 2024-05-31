# service-01. Slack API
________________________________________
※ 前提

```powershell
# powershellは標準でiwrのエイリアスにcurl指定しているので、そのままだとcurlが使えない（curl自体はWindows10 1809移行に標準で用意されてるのにね！）
del alias:curl
```

________________________________________
## 1. Slack App用意&設定～APIを叩いてみるまで
________________________________________
### 1.1. APIコール

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
## 2. Event、カスタムWorkflowStep
________________________________________
### 2.1. Eventコールバック

Eventコールバックサーバの用意と記述（reaction_addedの例)

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
4. Subscribe to bot eventsにreaction_addedを追加し、Save
5. Slack Appをワークスペースにre-install
```

エンドポイント側の処理

```text
1. リクエストBodyのjsonの内容を見てurl_verificationならchallengeをそのまま応答、それ以外なら続行
2. リクエストが偽物ではないことを検証
    - Slack App > Basic Information > App Credentials > Signing Secretでhash化された値が渡される
    - こちらも同じ手順でhash値を作って比較する
3. あとはやりたいことを自由に書く

※ 詳細はSlackApiSamplesのソースを参考の事
```

________________________________________
### 2.2. カスタムWorkFlowStep

そもそもSlackワークフローとは？

- 有料
- 入力フォームや通知などのStepを繋げてワークフローを構成する機能
    - Slack内通知には次のStepを開始するためのボタンをメッセージに含めることが可能
- ワークフローはスケジュールしたり手動で開始できる
- 各種SaaSと連携するためのサードパーティStepもある
    - issue発行
    - カレンダー登録
    - SMSやメール送信
    - シートへの行追加
    - ファイルコピー など
- Stepの自作も可能
- フォーム回答の全データはDL可能
    - 注意：この全データはAPIからアクセスする手段はない

Slackワークフローの例

1. ワークフローを開始し、条件を満たした人へDM通知
2. 入力フォームを実施してもらう
3. 結果をサードパーティStepで保存

よく使うステップ

Step名                                  |用途・役割・例
----------------------------------------|--------------------------------
Send a message to xx（旧Send a message）|チャンネルや相手へ通知や回答依頼
Collect info in a form（旧Send a form） |入力フォームの実施
サードパーティステップ（issue発行系）   |Bitbucket、Github、Gitlab、Jira
サードパーティステップ（Office系）      |Google WorkSpace、MS Office

-

Workflow stepの自作詳細（legacy版）

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
1. 「Eventコールバック」と同じ要領で、エンドポイントを用意
    - 同じプログラム内で処理するなら同じエンドポイントでよい
2. Slack App > Interactivity & Shortcuts > Enable
3. Request URLにエンドポイントを指定、Save
    - 前述の通り、Event Subscriptionsと同じエンドポイントでも良い
4. Slack App > Workflow Steps > Enable
5. step追加
6. Slack Appをワークスペースにre-install
7. Slack App > App Home > Home Tab > Enable
```

エンドポイント側の処理

```text
1. イベント処理(reaction_addedの例)の1、2と同様
2. 次の「メモ1：カスタムworkflow stepの処理詳細」に従って実装

※1 詳細はSlackApiSamplesのソースを参考の事
※2 bolt-js + node.js on Herokuの場合は、web: node app.jsで起動
※3 bolt-jsは妙に抽象化されているため、本来やる必要がある各処理が分かりづらい
```

メモ1：カスタムworkflow stepの処理詳細

```text
全体の流れ

Slack                                         Your Callback Server
  |                                                    |
  |1. ワークフロー作成開始                             |
  |2. 該当Stepの追加開始(workflow_step_edit)           |
  |--------------------------------------------------->|
  |                                                    |2.1. 構成モーダルを用意
  |        2.2. 構成モーダル表示API(views.open)        |
  |        <-------------------------------------------|
  |                                                    |
  |        2.2. に対する200 OK                         |
  |        ------------------------------------------->|
  |                                                    |
  |                                    2に対する 200 OK|
  |<---------------------------------------------------|
  |                                                    |
  |3. 該当Stepの構成確定(view_submission)              |
  |--------------------------------------------------->|
  |                                                    |3.1. 受信内容を元に保存内容を用意
  |        3.2. Step構成保存API(workflows.updateStep)  |
  |        <-------------------------------------------|
  |                                                    |
  |        3.2に対する200 OK                           |
  |        ------------------------------------------->|
  |                                                    |
  |                                    3に対する 200 OK|
  |<---------------------------------------------------|



Slack                                         Your Callback Server
  |                                                    |
  |4. ワークフロー実施。workflow_step_execute          |
  |--------------------------------------------------->|
  |                                                    |4.1. 任意の処理
  |                                    4に対する 200 OK|
  |<---------------------------------------------------|


workflow_step_edit
独自の構成モーダル自体を返却するためのコールバック
ワークフロー作成中にそのステップを「構成開始する際」に通知される
views.openを呼び出すことで構成モーダルを渡す

view_submission's workflow_step
独自の構成モーダルに入力された内容を検証・保存APIを呼び出すためのコールバック
ワークフロー作成中にそのステップが「Saveされる際」に通知される
workflow.updateStepを呼び出すことでそのStepの構成内容を保存する
「メモ2：手前のStepなどの入力内容を取得する方法」も参照の事

workflow_step_execute
ワークフロー実施時に、そのステップが実行される際に通知される
「メモ2：手前のStepなどの入力内容を取得する方法」も参照の事
```

メモ2：手前のStepなどの入力内容を取得する方法

```text
1. workflow_step_editで構成モーダルに欲しいパラメータの入力欄を用意する
2. ワークフロー作成時に入力欄へ「Insert a variable」で入力
3. view_submission's workflow_stepでペイロードから内容を取り出しinputsに含める
4. ワークフロー実施時にそれらが実際の値に置き換わる

※ 注意
変数はview_submission's workflow_stepで見ると{{...==...}}の形式になっている
- Time when step completed
    - 例：{{046e9b62-7533-4f44-9f7e-7a4127cefbc2==timestamp_completed}}
- Person who　submitted from XXXX
    - 例：{{046e9b62-7533-4f44-9f7e-7a4127cefbc2==user_submitted.name}}
- 手前以前のStepのoutputsされた項目 など
    - 例：{{046e9b62-7533-4f44-9f7e-7a4127cefbc2==5f6588ec-0525-44df-93fb-bf4fd38d0304==text}}
```

メモ3：bolt.jsの要約

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

________________________________________
### 2.3. Shortcut

設定

```text
1. 「カスタムWorkFlowStep」の1～3までと同様
2. Interactivity & Shortcuts > Create New shortcut > Global
```

エンドポイント側の処理

```text
1. ショートカット開始時、type=shortcutが通知される
    - callback_idがid
    - 例えば以下で、モーダル処理を開始する
        - https://slack.com/api/views.open
2. モーダルをsubmitした時、type=view_submissionが通知される
    - やりたいことをする
    - 正常に終わるなら200応答して終了

※ 詳細はSlackApiSamplesのソースを参考の事
```

コールバックの補足

```text
shortcut
shortcut起動時のイベントタイプ。今回の例ではここからviews.openを呼び出している

actionsプロパティが存在
フォーム上のボタンがクリックされた時など。actionイベントとして扱う
ボタンがクリックされた場合、actions.typeはbutton

view_submission's modal
モーダルを確定した際のイベントタイプ（ワークフローの構成モーダル除く）
```

________________________________________
## 3. Enterprise Gridエディション対応
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

その他

```text
# Enterprise Gridメモ
# OrG向けSCIM APIは必ず「Accept: application/json」「Authorization: Bearer xoxp-...」の2つのヘッダの付与が必要
EnterpriseGridのadmin APIについてもこちらが必要
```