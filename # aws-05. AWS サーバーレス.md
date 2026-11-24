# aws-05. AWS サーバーレス
________________________________________
## 1. 概要
________________________________________
サーバーレスとは

- 例えば、CloudFront + API Gateway + Lambda

従来との比較(やや不正確・曖昧だがわかりやすさ優先)

従来                                                        |サーバーレス
------------------------------------------------------------|-----------------
webサーバ 設定:ポート                                       |CloudFront, API Gatewayが管理
webサーバ 設定:DocumentRoot                                 |静的ファイルはS3、WebアプリはLambdaへマッピング
webサーバ 設定:locationマップ等（GETマップなども）          |CloudFront, API Gatewayが管理※1
webサーバ 設定:locationマップ等（50x、404など）             |Lambdaタイムアウト等はAPI Gateway, それ以外はアプリ側
webサーバ 設定:アプリサーバ(またはfastcgi)のアドレス        |API GatewayのLambda統合
webサーバ 設定:locationマップ等（秘匿すべきファイルのdeny） |概ね手動管理不要
webサーバ 設定:locationマップ等（静的ファイルのexpire等）   |CloudFront、S3側の設定
webサーバ 設定2:Webサーバ自体のworker等などのパラメータ     |なし(自動管理。Lambdaの同時実行数)
appサーバ 設定:ログ設定など                                 |CloudWatch Logs
appサーバ 設定:動作モードや動作詳細・メモリ・listen等       |Lambdaの設定(関数URLなど)
appサーバ 設定:読み込む拡張やライブラリ                     |成果物に含める※2
appサーバ 設定2:読み込む拡張やライブラリの設定ファイル      |成果物に含める※2
appサーバ インストール：拡張やライブラリのインストール      |ビルド時に成果物に焼き込む

※1 実際にはapp側=Lambdaにそのまま全部渡してそちら側で解決する構成が多め
※2 従来はサーバ立ち上げ時にインストールしたりしていたが、現在は依存込みの成果物（zipかコンテナイメージ）をビルド時に作る

具体例

典型的なSPAの場合、フロントはS3、サーバはコンテナ化してLambdaになりがち。いずれにしてもCloudFrontから同一ドメインにして、CORS等の設定は不要にする


________________________________________
## 2. AWS Lambda
________________________________________
※ 具象例として、Python FastAPI製のWebアプリコンテナを上げる想定の記載とする

________________________________________
### 2.1. Dockerfile(Webアプリ)側

定型文をWORKDIRより手前に追記(ビルドキャッシュが効く)

```text
COPY --from=public.ecr.aws/awsguru/aws-lambda-adapter:1.1.0 /lambda-adapter /opt/extensions/lambda-adapter
ENV AWS_LWA_PORT=8000
```

- Lambda Web Adapterは、Lambdaの呼び出しをHTTPリクエストに変換して同コンテナ内のlocalhost(127.0.0.1)のAWS_LWA_PORT指定ポートへHTTPを渡す
- 例えば以下のルールにしておけば、便利
    - Lambdaは小規模スクリプト(zip)または単コンテナを動かす仕組みなため、そもそもcomposeと無関係
    - Dockerfileは本番・開発で共通のことを書く。あるいは本番のことを書いておく
    - 開発環境はcompose側でENV(environment)、CMD(command)を上書きして動くようにする

________________________________________
### 2.2. AWS側 ECR

コンテナを上げる想定の場合、ECRがなければセットアップ

1. コンソールで「ECR」を開き、リージョンを東京（ap-northeast-1）になっていることを確かめる
2. 「リポジトリを作成」で、名前をfastapi-sample、可視性をプライベートにして作成する

________________________________________
### 2.3. AWS側 ECRへコンテナイメージのアップロード

1. CloudShellに以前アップしたzipと展開ディレクトリがあるなら削除
    - コンソールの左下「CloudShell」
        - cd ~
        - rm -rf fastapi-sample fastapi-sample.zip
2. CloudShellでビルドしてpushする
    1. Windows側で予め、アップロードするために、app/、docker/、(scripts/)、compose.yamlをまとめてzipにする(fastapi-sample.zip)
    2. コンソールの左下「CloudShell」>「アクション」>「ファイルのアップロード」> zip
    3. CloudShellで、次のコマンドを順に実行する

```bash
unzip fastapi-sample.zip -d fastapi-sample
cd fastapi-sample
# 今回のサンプルでは、Dockerfileがフォルダ直下にない想定なので-fの指定が必要(次の↓で一緒に表示されるサンプルは-fと--provenance=falseがない)
# ここでビルドした場合、x86_64イメージになる
docker build --provenance=false -f docker/api/Dockerfile -t fastapi-sample .
# 以下3行は、ECRのリポジトリ画面で「プッシュコマンドを表示」で表示されるもの(環境・構成・アカウントによって異なる)
#「330980311219」はリソースの持ち主のAWSアカウントID
aws ecr get-login-password --region ap-northeast-1 | docker login --username AWS --password-stdin 330980311219.dkr.ecr.ap-northeast-1.amazonaws.com
docker tag fastapi-sample:latest 330980311219.dkr.ecr.ap-northeast-1.amazonaws.com/fastapi-sample:latest
docker push 330980311219.dkr.ecr.ap-northeast-1.amazonaws.com/fastapi-sample:latest
```

________________________________________
### 2.4. AWS側 Lambda

Lambdaのセットアップ

1. Lambdaのコンソール（EventFunctions）>「関数の作成」>「コンテナイメージ」
2. 関数名をfastapi-sampleにし、「イメージを参照」> コンテナイメージを選択
3. カスタム設定 > その他の設定
    - アーキテクチャ: x86_64
    - カスタム実行ロール: OFF
        - 基本的なLambdaアクセス権限で新しいロールを作成（IAMロールを手で作る手順が不要）
    - 関数URL: ON
        - 認証: NONE
            - 認証なしだとURLを知っていれば誰でも呼び出せるので注意
        - 保存
4. 作成
    - KMSのエラー：作成時にKMSのエラーが出たら、少し待ってからもう一度「関数の作成」を押せば通る
5. 作成後、「設定」>「一般設定」
    - メモリを512MB
    - タイムアウトを30秒
6. 関数URLへ実際にアクセスして確認

________________________________________
## 3. AWS DynamoDB
________________________________________
### 3.1. 作成

1. コンソールで「DynamoDB」を開き、リージョンを東京（ap-northeast-1）になっていることを確かめる
2. 「テーブル」>「テーブルの作成」。以下例
    - テーブル名：items
    - パーティションキー：item_id、型は「文字列」
    - ソートキー：空のまま
3. テーブル設定の確認
    - 読み込み/書き込みキャパシティの設定:オンデマンド
4. 作成

________________________________________
### 3.2. ロールに許可追加

1. IAM >「ロール」> Lambda作成時に作られたIAMロール
    - 例：fastapi-sample-role-6k5diw0t
2. 「許可を追加」>「インラインポリシーを作成」>「JSON」
3. 以下を参考に貼り付けして、ポリシー名をfastapi-sample-items-tableにして作成
    - Resourceの値は実際に作成されたテーブルの情報にする

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:PutItem",
        "dynamodb:UpdateItem",
        "dynamodb:DeleteItem",
        "dynamodb:Scan"
      ],
      "Resource": "arn:aws:dynamodb:ap-northeast-1:330980311219:table/items"
    }
  ]
}
```

________________________________________
### 3.3. プログラム調整

例

- 環境変数DYNAMODB_TABLE_NAMEでテーブル名を指定できるようにしておく
- 環境変数DYNAMODB_ENDPOINT_URLで接続先を切り替えられるようにする
    - ローカルのDynamoDB Local用。Lambdaでは設定しない
- requirements.txtにboto3を追加する

________________________________________
### 3.4. Lambda調整

1. Lambdaのfastapi-sample >「設定」>「環境変数」>「編集」
    - 環境変数の追加例「DYNAMODB_TABLE_NAME」:「items」
2. DynamoDB接続に対応したコンテナイメージをzipアップ&再ビルド&push
    - 2.3. AWS側 ECRへコンテナイメージのアップロード
3. Lambda反映
    1. Lambda > fastapi-sample >「コード」タブ >「新しいイメージをデプロイ」
    2.「イメージを参照」で、latest（いちばん新しいpush）を選択、保存

________________________________________
## 4. Lambda、DynamoDBを完全に削除する場合
________________________________________
それぞれ削除

1. DynamoDBテーブル(items)
2. Lambda
3. ECRリポジトリ
4. 自動作成されたIAMロール
5. CloudWatch Logsのロググループ/aws/lambda/fastapi-sample