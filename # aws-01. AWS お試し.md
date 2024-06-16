# aws-01. AWS お試し
________________________________________
コードで学ぶAWS入門  
https://tomomano.github.io/learn-aws-by-coding/

AWSではじめる クラウド開発入門  
https://www.amazon.co.jp/dp/4839977607/

AWS IAM Identity Center の自動認証更新によるトークンプロバイダーの設定  
https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/sso-configure-profile-token.html
________________________________________
## 1. AWSルートアカウント準備～お試し
________________________________________
### 1.1. AWSルートアカウント作成

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
### 1.2. S3お試し

S3ダッシュボード

- サービス > ストレージ > S3

S3の追加

1. S3 > バケットの作成
    - your-org-name-sample-bucket

S3へファイル追加

1. S3 > 作成したバケット > フォルダの作成 : sample01
2. 上記フォルダを選択 > アップロード

________________________________________
### 1.3. EC2お試し

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
## 2. AWS CLIの用意と使い方
________________________________________
### 2.1. 概要とインストール

AWS CLIのインストール（例：v2.13.17）

1. 公式から環境に応じた内容でインストール
2. aws --version

AWS CLIの設定の保存先

- ユーザディレクトリ/.aws/

________________________________________
### 2.2. IAM Identity Centerユーザの場合

※ # aws-02. AWS アカウント関連 を参照してIAM Identity Centerユーザを作ってください

profileの作成

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
# プロファイルからアクセス可能なバケットをすべて表示
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
### 2.3. IAMユーザの場合

※ # aws-02. AWS アカウント関連 の IAMユーザを参照してください