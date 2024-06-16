# aws-02. AWS アカウント関連
________________________________________
## 0. 前提

「IAM Identity Centerユーザ」と「IAMユーザ」は別の存在

- IAM Identity Centerユーザ
    - SAML&SCIMに対応
    - 専用のログインポータルが用意される
    - Identity Center組織に所属
    - Identity Centerグループに所属
    - Identity Center許可セットのアタッチ
- IAMユーザ
    - 従来の方法
    - IAMグループに所属
    - IAM許可ポリシーのアタッチ

________________________________________
## 1. IAM Identity Centerの場合
________________________________________
### 1.1. 組織、グループ、ユーザ、許可セット

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
## 2. IAMユーザーとcredentialsの場合
________________________________________
### 2.1. IAMユーザ

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

________________________________________
### 2.2. アクセスキーの生成とアクセス

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