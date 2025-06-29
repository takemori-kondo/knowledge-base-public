# iam-03. SSOとSAML
________________________________________
## 1. AWSのALBによるSSO (リバースプロキシ型SSO)
________________________________________
### 1.1. ALB & Amazon CognitoによるSSO

https://aws.amazon.com/jp/blogs/aws/built-in-authentication-in-alb/

- CognitoならばSAML、ODICどちらもサポート
    - OIDCの場合は、IdP側のリダイレクト先はCognitoのユーザープールドメインを指定することになる
- 認証済みになると、X-AMZN-OIDC-ヘッダが付与されて後続のサービスへルーティング
    - 後続のサービスはそれらヘッダから認証されたユーザID等を知ることになる(JWT形式)

________________________________________
### 1.2. ALB & ODICによるSSO (Cognitoを介さない)

https://dev.classmethod.jp/articles/add-google-authenticate-your-webapp-on-alb/

サテライトオフィスなどの独自SSO解決サーバに委譲する場合もこっち

1. ALBにリスナーを追加

