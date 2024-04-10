# infra-01ex2. AWS
________________________________________
コードで学ぶAWS入門  
https://tomomano.github.io/learn-aws-by-coding/

AWSではじめる クラウド開発入門  
https://www.amazon.co.jp/dp/4839977607/

AWS IAM Identity Center の自動認証更新によるトークンプロバイダーの設定  
https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/sso-configure-profile-token.html

________________________________________
## 1. AWSアカウント準備～お試し
________________________________________
### 1.1. AWSアカウント作成

15.1. AWS アカウントの取得  
https://tomomano.github.io/learn-aws-by-coding/#sec:create_aws_account

アカウント作成

1. アカウント作成
2. アカウント名 > 設定 > デフォルトのリージョン > 東京
3. （IAM Identity Centerを有効にしたい場合など）
    - アカウント名 > 組織 > 組織を作成する

※ サービスから☆でお気に入り可能。リソースを作ったらお気に入りするとよい

代表的なサービス

- VPC
- EC2
- RDS
- S3
- FSx
- CloudWatch
- CloudFront
- IAM Identity Center
- IAM
- Billing

※ EC2、RDS、S3などは、お試し目的で12か月の間、少しだけ無料枠がある。色々試すとよい

________________________________________
### 1.2. S3の用意

S3ダッシュボード

- サービス > ストレージ > S3

S3の追加

1. S3 > バケットの作成
    - your-org-name-sample-bucket

S3へファイル追加

1. S3 > 作成したバケット > フォルダの作成 : sample01
2. 上記フォルダを選択 > アップロード

________________________________________
### 1.3. IAM Identity Centerユーザーを追加

IAM Identity Centerダッシュボード

- サービス > セキュリティ、ID、およびコンプライアンス > IAM Identity Center

IAM Identity Center(旧AWS SSO)ユーザの追加

1. アカウント名 > 組織を作成する
2. IAM Identity Center > 有効にする IAM Identity Center
    - リージョンを間違えないように
    - IAM Identity Centerダッシュボードが有効になる
3. IAM Identity Center > グループ > グループを作成
    - readonly-group
4. IAM Identity Center > ユーザ > ユーザーを追加
    - readonly
    - readonly-groupに参加
    - 追加されるとメールが飛ぶ。SSOポータルログイン
5. SSOからサインアウト
    - SSOログインはAWSアカウントログインとは独立している
6. IAM Identity Center > 許可セット > 許可セットの作成
    - 事前定義された許可セット
        - ReadonlyAccess
        - 他全てデフォルト
7. IAM Identity Center > AWS アカウント > 管理アカウント > ユーザーまたはグループを割り当て
    - readonly-group
    - ReadOnlyAccess
8. 同様の手順で、以下も作成して設定
    - poweruser-group
    - poweruser
    - PowerUserAccess

ログイン確認

1. IAM Identity CenterからログインURLをコピー
2. 作成したユーザでログイン
3. AWSのリソースが閲覧できることを確認
4. ReadOnlyAccessユーザーは作成に失敗することを確認

補足 IAM Identity Centerが複雑な理由

- IdP、SAML連携を想定している
- 複数のAWSアカウントを管理下にする事も想定している
- SSOユーザーは複数のAWSアカウントと紐づけ可能
- 上記のため、明示的な紐づけが必要

________________________________________
### 1.4. AWS CLIの用意と使い方

AWS CLIのインストール（例：v2.13.17）

1. 公式から環境に応じた内容でインストール
2. aws --version

AWS CLIの設定の保存先

- ユーザディレクトリ/.aws/

profileの作成（SSOユーザ）

```powershell
aws configure sso
# readonly-profile
# IAM Identity CenterにあるURL
# ap-northeast-1
# sso:account:access
# OAuthフローが開始される
#   注意：
#   既定のブラウザが既にSSOログインしていた場合、ログイン済になる
#   readonlyとpoweruserの2つのプロファイルを用意したい場合、SSOログアウトしておくこと
# ap-northeast-1
# json
# デフォルトの推奨名でOK
```

使用例

```powershell
# 保存したプロファイルの一覧を確認
aws configure list-profiles

# profile名が「ReadOnlyAccess-330980311219」あるいは「PowerUserAccess-330980311219」の例
# AWSアカウントに紐づいたバケットをすべて表示
aws s3 ls --profile ReadOnlyAccess-330980311219
aws s3 ls --profile PowerUserAccess-330980311219

# 特定のフォルダの中身を表示
aws s3 ls s3://takemori-sample-bucket/sample01/ --profile ReadOnlyAccess-330980311219
aws s3 ls s3://takemori-sample-bucket/sample01/ --profile PowerUserAccess-330980311219
```

SSO認証期限切れの対応

```text
# 前提：デフォルトでは8時間で認証が期限切れになる
# Error when retrieving token from sso: Token has expired and refresh failed

# 再認証（OAuthフローが開始される）
aws sso login --profile PowerUserAccess-330980311219
```

________________________________________
### 1.5. IAMユーザーとcredentialsの場合

IAMダッシュボード

- サービス > セキュリティ、ID、およびコンプライアンス > IAM

IAMユーザーの用意

1. IAM > ユーザー > ユーザーの作成
    - iam-poweruser
    - AWS マネジメントコンソールへのユーザーアクセスを提供する
        - IAMユーザーを作成します
    - ポリシーを直接アタッチする
        - PowerUserAccess
2. ログインの確認
    - IAM ユーザーとしてサインイン
        - アカウントID(例：330980311219)
        - iam-poweruser
        - パスワード

アクセスキーの生成とアクセス

1. 一旦ログアウトして、ルートユーザでログインしなおす
2. IAM > ユーザー > iam-poweruser
3. アクセスキーの生成
    - 上記のレコメンデーションを理解し、アクセスキーを作成します。
    - 次へ
    - アクセスキーの作成
4. 後述のテキストを作成し、名前をcredentialsにして保存
5. .awsフォルダ下に配置
6. profileを指定せずにAWS CLIで操作できることを確認

credentialsファイル

```text
[default]
aws_access_key_id = ...
aws_secret_access_key = ...
```

AWS CLI例

```powershell
aws s3 ls
```

________________________________________
### 1.6. EC2お試し

ダッシュボード

- サービス > コンピューティング > EC2

EC2お試し作成

1. EC2 > キーペア > キーペアを作成
    - keypair-sample01
    - RSA
    - .pem
2. EC2 > インスタンスを起動
    - vm-sample01
    - Amazon Linux 2023 AMI(無料利用枠)
        - 64ビット
    - t2.micro(無料利用枠)
    - keypair


________________________________________
## 2. VMとネットワーク構成
________________________________________
### 2.1. 概要

要約

1. VPC空間を用意
2. EC2を配置
3. VPCエンドポイントで他のAWSサービスと接続

典型的な構成

```text
Client <---> VPC Router <---> Private subnet

```

用語

用語         |意味
-------------|--------------------------------------
VPC          |≒VPN空間。この中にVM(=EC2)を配置
VPC end point|S3などをVPCと繋ぐための設定（※）

-

※ S3などはGlobalである（Private Network内ではない）
VPC end pointを使わない場合、通信路がAWS外経由になる
上記を防ぎ、AWS内で通信路を完結するための設定

________________________________________
## 3. S3
________________________________________
Amazon S3 のよくある質問  
https://aws.amazon.com/jp/s3/faqs/

オブジェクト キー名の作成  
https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-keys.html

Amazon S3 からの HTTP 500 または 503 エラーをトラブルシューティングするにはどうすればよいですか?  
https://repost.aws/ja/knowledge-center/http-5xx-errors-s3

________________________________________
### 3.1. よくあるトラブル

1. 単純な方式では1アップロード5GB制限
    - それ以上の場合はマルチパートオプションが必要
2. フォルダやファイル名で避けたほうが良い文字
    - 後述
3. 高負荷でなくても極まれに5xxを返すことがある
   - 再試行すればよい
   - 経験則では、10万～20万回アップロードに1回程度？

________________________________________
### 3.2. ファイル名で避けたほうが良い文字

```text
\{^}%`]'>[~<#|
&$@=;/:+,?
制御文字
2つ以上連続するスペース
```

________________________________________
### 3.3. よく使うコマンド例

```powershell
# profile名が「PowerUserAccess-330980311219」の例

# AWSアカウントに紐づいたバケットをすべて表示
aws s3 ls --profile PowerUserAccess-330980311219

# 特定のフォルダの中身を表示
aws s3 ls s3://takemori-sample-bucket/sample01/ --profile PowerUserAccess-330980311219

# コピー
aws mv cp sample-file02.txt s3://takemori-sample-bucket/sample02/

# 移動
aws s3 mv sample-file03.txt s3://takemori-sample-bucket/sample02/
```