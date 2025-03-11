# unity08. Steamworks と Unity
________________________________________
UnityとUEでSteam対応のあれこれ - スタッフブログ｜株式会社ジーン xeen Inc.  
https://www.xeen.co.jp/staffblog/2023/02/blog-20230214.html

Steam  
https://docs.unity.com/ugs/ja-jp/manual/authentication/manual/platform-signin-steam

ドキュメントホームページ (Steamworks ドキュメント)  
https://partner.steamgames.com/doc/home

Steamworks API概要 (Steamworks ドキュメント)  
https://partner.steamgames.com/doc/sdk/api#thirdparty

Steamworks APIのサンプルアプリケーション（SpaceWar）  
https://partner.steamgames.com/doc/sdk/api/example

Example project for Steamworks.NET (↑をSteamworks.NETを利用して再実装したもの)
https://github.com/rlabrecque/Steamworks.NET-Example

________________________________________
## 1. Steam側にアカウント追加&ログイン
________________________________________
既に組織が存在する場合

1. 組織管理者から招待してもらう
2. 作業用のSteamアカウントがない場合は会社メールなどでアカウント作成
3. Steamアカウントにログインしている状態で承諾
4. 下記にアクセスできればOK
    - https://partner.steamgames.com/dashboard

________________________________________
## 2. Steamworks.NETおよびテストプレイ
________________________________________
### 2.1. Steamworks.NET 概要

要約

- SteamworksはWeb API提供である。なお公式はC++しかヘルパライブラリを用意していない
    - Unity向けヘルパとして、サードパーティを2つ紹介している
- Unity公式はサードパーティのSteamworks.NETを紹介している
- Steamworks.NETは、公式のSDKと同じような使用感になるようなヘルパライブラリである

Steamworks.NET - Installation  
https://steamworks.github.io/installation/

Steamworks.NETの追加と利用

1. Package Manager > Install package from git URL
    - https://github.com/rlabrecque/Steamworks.NET.git?path=/com.rlabrecque.steamworks.net
    - 正常に終わると、steam_appid.txtがプロジェクトルートに追加される。IDは自身のプロジェクトのIDのプロジェクトを使うこと
        - このファイルはBOMなしUTF-8にすること
2. 必要に応じてasmdefに参照追加
    - com.rlabrecque.steamworks.net
3. 別途、サンプルのSteamManager.csを追加
    - https://github.com/rlabrecque/Steamworks.NET-Example/blob/master/Assets/Scripts/Steamworks.NET/SteamManager.cs
4. 必要に応じてさらにサンプルを参考にしたり、Steamworksの公式情報を見ながら良しなに実装

________________________________________
### 2.2. 実績 API

下記を参照のこと

ステップバイステップ：実績 (Steamworks ドキュメント)  
https://partner.steamgames.com/doc/features/achievements/ach_guide

________________________________________
### 2.3. Stats API

下記を参照のこと

ステップバイステップ：データ (Steamworks ドキュメント)  
https://partner.steamgames.com/doc/features/achievements/stats_guide

________________________________________
### 2.4. アップロードとテスト

UnityのビルドをSteamにアップロードする方法 - ヘンゼルのパンくず  
https://tmls.hatenablog.com/entry/2021/03/22/190540

1. AppIDをsteam_appid.txtに適用してexeビルド
2. Steamworks SDKをダウンロード、解凍、さらに解凍
3. Unityでビルドしたファイルを、以下にコピー
    - sdk\tools\ContentBuilder¥content
4. SteamPipeGUI.exe 起動して適切に設定
    - App ID
    - Build説明
    - Add Depot
        - sdk\tools\ContentBuilder¥content の位置の指定
    - sdk\tools\ContentBuilder の位置を指定
    - アカウントID/Pass
5. Upload
    - シェルスクリプトが順次実施されるが、ログイン時「Steam Guard Code」と言われるのでワンタイムコードを入れる
6. Steamworks >テクニカルツール Steamworks設定を編集に移動。以降この画面でいくつか確認や設定を行う
7. SteamPipe > Build を確認し、アプリブランチをdefaultにし、さらに「今すぐビルドをライブに変更」する
    - アカウントに電話番号が登録されていない場合、警告が出るため登録して2段階認証を有効にする
8. インストール > インストール全般 > 起動オプションに、Unityビルド時に作成されたexe名を指定
9. 公開 > Steamに公開 > 本当に公開する
10. インストール
    - Steamクライアント側がインストール済扱いになってしまっている場合、アンインストール（終わらない）してPC再起動すれば一応どうにかなる

________________________________________
### 2.5. Unity EditorとSteamクライアント

プレイ中になり続ける問題

Steamworks.NET - よくある質問  
https://steamworks.github.io/faq/

Test (unity) app does not shut down properly. · Issue #13 · rlabrecque/Steamworks.NET  
https://github.com/rlabrecque/Steamworks.NET/issues/13

- Unity上で実行すると、Unityや関連するプロセスのいずれか実行中である限りプレイ中になる問題
- これはSteamクライアント側の仕様が関わっているため、回避はできない