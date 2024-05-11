# infra-03. OAuth
________________________________________
## 1. 仕組み
________________________________________
Twitter OAuthが実現されている例：Qiita

```text
1. qiita.com（Client）  ：Twitter（でLogin）ボタンを押下
                          https://api.twitter.com/oauth/authorize
                          にリダイレクトされる
2. twitter.com（Server）：「Qiitaにアカウントへのアクセスを許可しますか？」
                           ユーザ/パスを入力し、連携アプリを認証を押下
```

Azure ADでのOAuth

```text
1. 予めAzure側でClient IDを用意
2. 以下の2つを用意
    - 非認可状態でClientサイトにアクセスしたらユーザー承認ページへのURLを作成してリダイレクトさせる機構
    - SSLなCallbackページ（認可コードやOAuth Access tokenが返却されてくる）
3. 非認可状態でClientサイトにアクセスしたら、ユーザー承認ページに以下パラメータを付与してリダイレクトさせる
https://login.microsoftonline.com/common/oauth2/authorize?
    - response_type=code
    - client_id=<APIキー相当>
    - redirect_uri=<callbackページ>
    - scope=
    - state=乱数（セッション側でも保持して、callbackページで値が同一か検証する（csrfトークンの一種））
4. 成功した場合、callbackページに認可コードが返却される
5. 認可コードを利用して、OAuth Access tokenをさらに取得しに行く（詳細は省略）

※ AzureのOAuthは、認可コード取得→OAuth Access token取得の方式を採用している
※ AzureのOAuthは、APIキー・APIキーシークレットのペアではなく、単にClient IDである
```

SlackやTwitterでAPIを使用するまでの流れで理解するOAuth

```text
1. 開発者サイトにて、開発者登録、App生成、App設定（Scope、想定するCallbackページ一覧）
    - Appに対するAPIキー、APIキーシークレットが生成される
        - Twitterの場合：consumer key、consumer key secret
        - Slackの場合：clinet id、client secret
2. 以下の2つを用意
    2.1. ユーザー承認ページへ移動するためのリンクを生成する仕組み
        - 必要なパラメータ
            - APIキー
            - APIキーシークレット
            -（request_token。Twitterの場合必要。直前に取得する）
            - callbackページ
    2.2. SSLなCallbackページ（OAuth Access tokenが返却されてくる）
3. ユーザーが承認すると、CallbackページにOAuth Access tokenを含めてcallbackされるので、それを取得する
4. OAuth Access tokenを使用して、APIを使用できる

※ NomralなSlack Appを開発時なら、
自Workspaceへインストール（＝OAuth Access Token取得）のために、Callbackページの用意や設定は不要
開発者サイトが代行してくれる
```

要素まとめ

```text
[必須要素]
OAuth authorization server   : ホストの一部分。ユーザが認証することでOAuth access tokenを返すサーバ
    OAuth authorization code : 認可コード(ワンタイムコード)
    OAuth access token       : アクセストークン
OAuth resource server        : ホストの一部分。APIサーバ
OAuth Client                 : サードパーティアプリ
    API key                  : 予めホストに用意してもらう（Confidential Client形式の場合）
    API key secret           : 予めホストに用意してもらう（Confidential Client形式の場合）
    Scope                    : サードパーティアプリが要求する権限リスト
    Callbackページ           : サードパーティアプリがOAuth access tokenを受け取るページ
Resource owner(end user)     : ユーザ

[補足]
・リフレッシュ機構がある場合もある
・認可コード取得→OAuth access token取得は表立って登場しない実装も多い
・トークンは必ずしもBearerヘッダに付与してリクエストするとも限らない
 （Slackのように、パラメータで要求される例もある）

※ Twitter、Slack、Azure ADの例で分かる様に、細かい仕様には差異がある
リファレンスを読むこと
```