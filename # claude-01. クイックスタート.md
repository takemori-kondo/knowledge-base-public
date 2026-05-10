# claude-01. クイックスタート
________________________________________
Claude Code の概要   
https://code.claude.com/docs/ja/overview

クイックスタート  
https://code.claude.com/docs/ja/quickstart

一般的なワークフロー  
https://code.claude.com/docs/ja/common-workflows

設定 > 使用量  
https://claude.ai/settings/usage

使用制限のベストプラクティス   
https://support.claude.com/ja/articles/9797557-使用制限のベストプラクティス

________________________________________
## 1. インストール
________________________________________
### 1.1. アカウント用意

使用制限の目安

プラン    |5時間limit|週間limit
----------|----------|-----
個人Pro   |約44k     |約500k
個人Max100|???       |???

.

________________________________________
### 1.2. CLI版の場合

CLI版インストール

```powershell
# 最新の方法は「Claude Code の概要」で確認してください
# 古い方法だと最新機能が使えない場合があります
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
irm https://claude.ai/install.ps1 | iex
# ユーザー環境変数開いてPATHを追加
## %USERPROFILE%\.local\bin
# powershell再起動
```

CLI版起動の例

```powershell
$dir = "D:\Dropbox\repos_unity\Uft.AdvTools\Assets\Uft.AdvTools\Runtime"
cd $dir
claude
# 初回はOAuthでログインすることになる

# 最新セッションを再開したい場合
# claude -c

# remote-control受け入れを起動する場合
# claude remote-control --name "Foo"
```

________________________________________
### 1.3. GUI版の場合

インストール

1. アプリ版をインストール
2. 起動。初回はOAuthでログインすることになる

________________________________________
## 2. 初心者向けガイダンス
________________________________________
### 2.1. remote-control

- Pro、Maxでのみ使える
- 古いCLIは未対応
- 上手くいかない場合、logoutしてログインし直してみる
- 最低1回は対象フォルダで通常のclaudeで開始している必要あり

________________________________________
### 2.2. 初歩的なプラクティス

基本

1. 最初に探索させる。探索範囲は適度に限定する
    - 読む必要がある範囲の明示
2. 指示は具体的・明確・シンプルなものにする(コンテキストの明確化)
    - 具体的な指示、解釈が不要な指示
3. コンテキストが満タンになると圧縮され、実質忘れてしまう
    - 文脈（コンテキスト）はすぐいっぱいになる。文脈に頼りすぎない
    - 最低限常に覚えておきたい内容は、CLAUDE.md に明記
4. CLAUDE.mdは200行以内にする
5. 必要に応じてプランモードを使用する

応用

1. claude code自身が検証出来るようにする
    - ビルド、ビルド結果が読み取れるようにする
    - 実行、実行中のログの読み取りが出来るようにする
    - 具体的な例外やエラーを読み取れるようにする
2. 定型作業はフックやスキル化する
    - スクリプト化出来そうなレベルはフック
    - 曖昧だが定型作業はスキル
3. サブエージェント化する