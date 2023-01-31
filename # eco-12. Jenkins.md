# eco-12. Jenkins
________________________________________
日本Jenkinsユーザ会  
https://jenkins.jp/

日本語 : Jenkins  
https://wiki.jenkins.io/display/JA/Jenkins

________________________________________
## 1. 用意と初期設定
________________________________________
### 1.1. 用意

※ 通常はビルドサーバと本番環境と同じOSにするのが無難

Java Downloads | Oracle 日本  
https://www.oracle.com/jp/java/technologies/downloads/#jdk17-windows

1. Javaランタイムの用意（2023年現在の要求は11～17なので実際に使用するのはJDK）
2. ダウンロード：jenkins.war


________________________________________
### 1.2. 起動と初期設定（war形式の場合）

1. 起動

```powershell
cd ファイルのある場所
java --version
# ポート指定して起動する場合は以下
# java -jar jenkins.war --httpPort=42423
java -jar jenkins.war

```
2. 初回起動時、出力を追うと以下の記述がある

```text
# ...
Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

3634292020cf411eb58db4756f9963e0

This may also be found at: C:\Users\<現在のユーザ>\.jenkins\secrets\initialAdminPassword
# ...
```

3. http://localhost:8080
4. Unlock Jenkinsページ
    - 上記パスワードを入力
5. Customize Jenkinsページ
    - Install suggested plugins
6. Create First Admin Userページ
    - Skip and Continue as admin
7. Instance Configurationページ
    - 42423に変更
        - localhostとは関係なく、メール通知に記載するURL
8. 完了。いったん終了
    - シェルでCtrl+C
9. 再起動
    - java -jar jenkins.war --httpPort=42423
10. アクセス
    - http://localhost:42423/
    - admin/前述のパスワード
11. Jenkinsの管理 > システムの設定
    - システム管理者のメールアドレス
        - 設定
    -  E-mail 通知
        - SMTP：smtp-mail.outlook.com
        - E-mailのサフィックス：空欄
        - 高度な設定
            - Use SMTP Authentication：チェック
            - ユーザー名：メールアドレス
            - パスワード：パスワード
            - UseTLS：チェック
            - SMTPポート：587
            - 返信先アドレス：メールアドレス
            - 文字セット：UTF-8
            - 「メールを送信して設定を確認」があるので確認する
    - 保存

________________________________________
## 2. ジョブの構築
________________________________________
ビルドジョブのベストプラクティス

```text
1. ジョブ名は英数字のみ
2. 「ソースブランチ」と「デプロイ先（環境設定）」との独立
    - 「ソースブランチ」は環境設定の切り替えは想定せず、プログラムバージョンのみに焦点を当てる
    - 「デプロイ先（環境設定）」を切り替えられるよう、上書き・書き換えバッチを用意する
3. 場合によってはさらに環境設定を制御するパラメータも用意
    - 例えば認証タイプ。実際のところデプロイ先と1:1で対応していることも少なくないが、分けておくと良い
4. production環境が設定が機密情報の場合などは、少なくともソースコード側に用意することが出来ない
    - 別途機密情報用のなんらかしらの管理と差し替えが必要
```

典型的な例

```text
1. 新規ジョブ作成 > パイプライン
2. 以下のように設定

- ビルドのパラメータ化 on
    - SourceBranch(master/dev/production)
    - DeployTarget(UAT/OnpreProduct/AwsProduct)
- 古いビルドの破棄 on
    - Log Rotation
    - 10個
- パイプライン
    - Pipeline script from SCM (Source Control Management)
        - SCM - Git - リポジトリ
            - ビルドするブランチ、追加処理（Check out to a sub-directory:Jenkins）
        - Script Path
            - .gitがあるパスからjenkinsfileまでの相対パスを指定する
            - 大文字小文字区別するので注意。Jenkinsfileとjenkinsfileなど
```

Groovyサンドボックスの注意点

```text
この仕組みを理解していないと？？？となる
ジョブの設定内容が下記のどちらかの場合、Groovyはサンドボックスになる
この時、ほとんどのJava関数は標準ライブラリ含めビルド保留対象になる
実行時にぺンディングされ、adminの許可が必要（Paths.getさえもimportしても無許可で使えない！）

- Pipeline scriptでサンドボックスにチェックが入っている場合
- Pipeline script from SCM

許可は以下にリンクから可能
ジョブの状態 > ビルド番号 > コンソール出力 > 出力内容にリンクがある

※ 保留されたビルド番号は中止されるため、新しくビルドが必要
```

________________________________________
## 3. Jenkinsfile
________________________________________
### 3.1. Groovy

The Apache Groovy programming language - Documentation  
https://groovy-lang.org/documentation.html

要約

- Java文法ベースのスクリプト言語
- クラスを書かなくてよい
- 型の代わりにdefとしてよい
- 省略可能：セミコロン
- 省略可能：関数呼び出しの()
- 文字列リテラルに${var}が可能。変数展開しない文法として'での文字列リテラルも可能
- 配列リテラルとして[1,2,3]をサポート。足し引き可能
- 連想配列リテラルとして[foo:1, bar:2]。足し引き可能
    - [(varA):1, bar:2]とやれば、変数の中身をキーにする事が可能
- foreachを書く際、「:」の代わりに「in」でもよい
- @Library(...)
- 以下は暗黙にimportされる
    - import java.lang.*
    - import java.util.*
    - import java.io.*
    - import java.net.*
    - import groovy.lang.*
    - import groovy.util.*
    - import java.math.BigInteger
    - import java.math.BigDecimal
- その他差異はあるが、概ね上記を抑えておけばよい

________________________________________
### 3.2. 主な定義済み変数と拡張構文

env変数  
https://www.jenkins.io/doc/book/pipeline/jenkinsfile/#using-environment-variables

params変数  
https://www.jenkins.io/doc/book/pipeline/jenkinsfile/#handling-parameters

@Library annotation  
https://www.jenkins.io/doc/book/pipeline/shared-libraries/

- Jenkinsの管理 > システムの設定 > Global Pipeline Libraries

pipeline{} (次節を参照のこと)

$class: 'GitSCM'  
https://www.jenkins.io/doc/pipeline/steps/params/gitscm/#class-gitscm

________________________________________
### 3.3. pipeline{}の書き方

Jenkinsfileの概要  
https://www.jenkins.io/doc/book/pipeline/jenkinsfile/

pipeline{}の概要  
https://www.jenkins.io/doc/book/pipeline/#declarative-pipeline-fundamentals

ステップの実引数の書き方  
https://www.jenkins.io/doc/book/pipeline/jenkinsfile/#optional-step-arguments

pipline{}の詳細  
https://www.jenkins.io/doc/book/pipeline/syntax/

主なpipeline内での操作（stagesなどの大構造除く）

|操作                           |要約
|-------------------------------|----------------------------
|script {}                      |groovyスクリプトを記述できる
|checkout([$class:'GitSCM',...])|gitをチェックアウトできる
|dir("カレントディレクトリ") {} |{}内はそのカレントディレクトリで実行する
|echo "文字列"                  |echoできる
|bat "任意のシェルコマンド"     |任意のコマンドを実行できる

-

________________________________________
### 3.4. jenkninsfileのデバッグ

とりあえず直接貼り付けて、ビルドを実施してビルド番号 > コンソール出力を見ればよい