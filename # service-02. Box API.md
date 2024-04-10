# service-02. Box API
________________________________________
※ 前提

```powershell
# powershellは標準でiwrのエイリアスにcurl指定しているので、そのままだとcurlが使えない（curl自体はWindows10 1809移行に標準で用意されてるのにね！）
del alias:curl
```

________________________________________
## 1. 通常版の要約
________________________________________
### 1.1. Box App用意&設定～APIを叩いてみるまで

```powershell
# 1. アカウント作成
# 2. https://app.box.com/developers/console
# ※ アカウント設定で日本語表示可能
# 3. [アプリの新規作成] > [カスタムアプリ] > [ユーザー認証(OAuth 2.0)], AppName=BoxAppSample
# 4. アプリケーションスコープの設定
#    - [構成]タブ > [アプリケーションスコープ] [Boxに格納されている...読み取り]チェック (フォルダやファイルの閲覧に必要)
#    - [構成]タブ > [アプリケーションスコープ] [Boxに格納されている...書き込み]チェック (フォルダやファイルの変更、トークンのダウンスコープに必要)
#    - 変更の保存
# 5. 開発者トークンの再生成（[取り消し] > [開発者トークンを生成]）
# 6. テスト

curl https://api.box.com/2.0/users/me -H "authorization: Bearer 2rTyzLeHnLqE3S3ApRocUmLF1LMDwD3q"
```

※1 正規トークンは後回しにして、開発用トークンで試すことが可能

※2 正規トークンの場合、Slack Enterpriseのように資格情報トークンを受け取るURLエンドポイントが必要になる

________________________________________
### 1.2. フォルダ内容やファイル概要の取得

```powershell
# 1. 普通にBoxサービスからフォルダ作成 > フォルダに移動し、URL末尾のIDをメモ（例：199222140763）
# 2. 適当にファイルをアップ
# 3. テスト
curl https://api.box.com/2.0/folders/199222140763 -H "authorization: Bearer 2rTyzLeHnLqE3S3ApRocUmLF1LMDwD3q"

# 4. アップしたファイルをBox上で開き、URL末尾のIDをメモ（例：1166133852855）
# 5. テスト
curl https://api.box.com/2.0/files/1166133852855 -H "authorization: Bearer  2rTyzLeHnLqE3S3ApRocUmLF1LMDwD3q"
```

________________________________________
### 1.3. UIエレメントのプレビューワー1

プレビューワーの利用（トークンを直接埋め込み（本番では禁止））

1. slide1.pptxをアップ（URLのID例：1166247367445）
2. BoxApp設定
    - [構成]タブ > [CORSドメイン] > アップしたWebサイトのドメイン
    - 開発者トークンの再生成（[取り消し] > [開発者トークンを生成]）
3. htmlのaccessTokenに開発者用トークンを直接記載（本番運用してはならない）
4. htmlをWebサイトにアップ
5. パワーポイントが閲覧できること確認する

```html
<!DOCTYPE html>
<html lang="en-US">
  <head>
    <meta charset="utf-8" />
    <title>Box Content Preview Demo</title>
    <script src="https://cdn.polyfill.io/v2/polyfill.min.js?features=Promise"></script>
    <link rel="stylesheet" href="https://cdn01.boxcdn.net/platform/preview/2.81.0/en-US/preview.css" />
    <script src="https://cdn01.boxcdn.net/platform/preview/2.81.0/en-US/preview.js"></script>
    <style>
        .bp-container {
            height:800px;
            width:100%;
        }
    </style>
  </head>
  <body>
    <script>
const { Preview } = Box;
const preview = new Preview();
const fileId = "1166247367445";
const accessToken = "WqukkKzrKA2jxk6qnCHiKXw7tmCDZjYs";
const options = {}; // {canDelete: false};
preview.show(fileId, accessToken, options);
    </script>
  </body>
</html>
```

________________________________________
### 1.4. UIエレメントのプレビューワー2

トークンのダウンスコープ  
https://ja.developer.box.com/guides/authentication/tokens/downscope/

対象ファイル用トークンの発行

```powershell
curl -i -X POST "https://api.box.com/oauth2/token" `
     -H "Content-Type: application/x-www-form-urlencoded" `
     -d "subject_token=WqukkKzrKA2jxk6qnCHiKXw7tmCDZjYs" `
     -d "subject_token_type=urn:ietf:params:oauth:token-type:access_token" `
     -d "scope=base_preview item_preview" `
     -d "resource=https://api.box.com/2.0/files/1166247367445" `
     -d "grant_type=urn:ietf:params:oauth:grant-type:token-exchange"
```

htmlのaccessTokenを受け取ったトークンに変更

________________________________________
### 1.5. コンテンツエクスプローラ

コンテンツエクスプローラ  
https://ja.developer.box.com/guides/embed/ui-elements/explorer/

スコープ  
https://ja.developer.box.com/guides/api-calls/permissions-and-errors/scopes/#ダウンスコープ用のスコープ

基本的な流れはプレビューワーと同様だが、Explorer用トークンは以下で生成が妥当


```powershell
curl -i -X POST "https://api.box.com/oauth2/token" `
     -H "Content-Type: application/x-www-form-urlencoded" `
     -d "subject_token=WqukkKzrKA2jxk6qnCHiKXw7tmCDZjYs" `
     -d "subject_token_type=urn:ietf:params:oauth:token-type:access_token" `
     -d "scope=base_preview item_preview base_explorer" `
     -d "grant_type=urn:ietf:params:oauth:grant-type:token-exchange"
```

サンプル


```html
<!DOCTYPE html>
<html lang="en-US">
  <head>
    <meta charset="utf-8" />
    <title>Box Content Preview Demo</title>
    <script src="https://cdn.polyfill.io/v2/polyfill.min.js?features=Promise"></script>
    <!-- <link rel="stylesheet" href="https://cdn01.boxcdn.net/platform/preview/2.81.0/en-US/preview.css" /> -->
    <!-- <script src="https://cdn01.boxcdn.net/platform/preview/2.81.0/en-US/preview.js"></script> -->
    <link rel="stylesheet" href="https://cdn01.boxcdn.net/platform/elements/16.0.0/en-US/explorer.css"/>
    <script src="https://cdn01.boxcdn.net/polyfills/core-js/2.5.3/core.min.js"></script>
    <style>
        .bp-container {
            height:800px;
            width:100%;
        }
    </style>
  </head>
  <body>
    <script src="https://cdn01.boxcdn.net/platform/elements/16.0.0/en-US/explorer.js"></script>
    <script>
const { Preview } = Box;
const contentExplorer  = new Box.ContentExplorer();
const folderId = "199338538981";
const accessToken = "sAEFrewCuWq7EFwuEHMAiYqzzuDzNLwz";
const options = { container: ".container" }
contentExplorer.show(folderId, accessToken, options);
    </script>
  </body>
</html>
```

________________________________________
## 2. Box CLIを使用してOAuthしてみる
________________________________________
### 2.1. Box CLI

概要

```text
OAuthのリフレッシュを自動的に行うように内蔵されたヘルパ
curlで直接呼び出す場合との違いは以下

1. 初回OAuthフローするためのコマンドもある（box login）
    - BoxApp側の[OAuth 2.0リダイレクトURI]は下記設定にする必要があり
        - http://localhost:3000/callback
        - 両トークン初回取得直後に両トークンのリフレッシュが行われるため実害はない
2. 内部的に両トークンの最新化が自動で行われる
    - 注意：BoxのOAuthの共通仕様としてリフレッシュトークン自体は60日で切れる
        - よって60日以上CLIを使用しなかった場合、再度box loginが必要になる
3. アクセストークンの指定が省略出来る
4. パラメータ指定方法は当然異なる
```

設定とインストール

```text
1. 通常通り、Box App側の用意&設定。ただし[OAuth 2.0リダイレクトURI]を以下に設定
    - http://localhost:3000/callback
        - （初期設定のhttps://app.box.comは消す必要がある）
2. Box CLIをインストール
    - 「PATH not updated, original length xxxx > 1024」と出る場合は手動でPATH追加
        - Box CLIインストーラ(が使用してるNSIS)のバグ
        - C:\Program Files\@boxcli\bin\
```

使い方

```powershell
# PATHが通っていることを確認
box --version

# 初回OAuthフローの実施
# 1. 予めブラウザでBoxにログインしておく
box login -n BoxAppSample
# 1. クライアントID入力
# 2. クライアントシークレット入力
# 3. ブラウザのタブが開き、OAuth許可を求めるフェーズの画面になる。許可する
# 4. 許可が完了すると、BoxCLIでAPIが利用できるようになる

# トークン利用の確認
box users:get me
```

両トークンの保存場所

```text
# https://github.com/box/boxcli/blob/d94ab35a38938daf4edbbd134774a3809facecbd/src/token-cache.js
# 上記ソースを読む限り、両トークンは下記に保存されている（2023年3月現在）
C:\Users\<あなたのUser>\.box\example_name_token_cache.json
```

________________________________________
### 2.2. 手動で初回OAuthを解決する

略

- Slack EnterpriseGrid とほぼ同様の手順が必要になるため面倒（Webサイトの用意など）
- 自分用BoxAppで自分用に初回OAuthするなら、BOX CLIで用意するのが楽

________________________________________
### 2.3. トークンのリフレッシュ

API

```powershell
# 両トークンがリフレッシュされる。古いリフレッシュトークンは無効化される
curl -i -X POST "https://api.box.com/oauth2/token" `
     -H "Content-Type: application/x-www-form-urlencoded" `
     -d "client_id=[CLIENT_ID]" `
     -d "client_secret=[CLIENT_SECRET]" `
     -d "refresh_token=[REFRESH_TOKEN]" `
     -d "grant_type=refresh_token"
```

レスポンス例

```json
{
    "access_token":"JIvF3JKUKztl1bbJWV6AXA6rDesJAGXw",
    "expires_in":3995,
    "restricted_to":[],
    "refresh_token":"qRoN5TZfdfvO1PzeReWGtWmJ2f61t1bJfbOrN1WaUsAfcrERBfXvGe5kV8ayWefD",
    "token_type":"bearer"
}
```

注意

```text
BOX CLIのcacheにあった値を利用してこれを行った場合、当然cacheファイルは更新されない
よってBOX CLIは直近のアクセストークンが期限切れになり次第、リフレッシュ出来ずにエラーを吐く
（なおリフレッシュ時、古いリフレッシュトークンが無効化されるまでタイムラグがある）
（恐らくBox側が分散システムで無効化するまでにタイムラグが発生しているためと思われる）
```