# iam-01. Identity and Access Management
________________________________________
※ 一連のページ群では、SSO、SAML、OIDCの総称としてIAMという用語を使用します  
あまり一般的とは言い難いですが、良い単語が存在しないためご了承ください

OAuth、OpenID Connect、SAMLの違い  
https://www.okta.com/jp/identity-101/whats-the-difference-between-oauth-openid-connect-and-saml/

ID およびアクセス管理 (IAM) とは  
https://learn.microsoft.com/ja-jp/entra/fundamentals/introduction-identity-access-management
________________________________________
## 1. 全体概要
________________________________________
### 1.1. IAMという用語について

歴史

- IAMと言うとAWSを思い浮かべるが、それ以前（2000年前後）から用語として存在していた
- 元々はIBM、Microsoft、Oracleといった企業がその概念を提唱し始めた
- ただし、OIDCやSAML2.0といったプロトコルが成立しクラウド時代になるまで、フンワリ用語だった

________________________________________
### 1.2. OIDCの例

認可&リソースサーバの例

- Google
- Apple
- Github
- X(Twitter)
- Facebook

クライアントの例

- Evernote個人向け(Google, Apple)
- DropBox個人向け(Goole, Apple)
- Box個人向け(Google)
- Pixiv(Goole, Apple, X, Facebook)
- Qiita(Google, Github, X)

________________________________________
### 1.3. SAMLの例

IDaaS(IdP)の例

- Microsoft Azure AD SSO
- Google Workspace SSO
- Zoho Office Suite

SPの例

- Microsoft 365 Business Basic～
- Microsoft 365 Apps for business
- Google Workspace Business Starter～
- Zoho Office Suite Business～
- Salesforce
- Slack Business+～
- Dropbox Business Advanced～
- Box Business～
- GitHub Enterprise
- Jira/Confluence Enterprise
- Zendesk Team～
- Zoom Business～

________________________________________
## 2. 用語
________________________________________
用語の要約

用語                                             |意味
-------------------------------------------------|--------------------------
IAM(Identity and Access Management)              |SSO、SAML、OIDCなどの総称
SSO(Single Sign On)                              |利用者が達成したい事（複数のクラウドサービスを1つのアカウントでログインしたい）
IDオートプロビジョニング                         |利用者が達成したい事（IdPからSPへ組織アカウント情報を自動同期してほしい）

-

OAuth/個人アカウント関連

用語                                             |意味
-------------------------------------------------|--------------------------
OAuth                                            |あるサービスが他サービスに認可を与えるための仕組み。特に理由がなければOAuth2.0またはOIDCを指す
OIDC(OpenID Connect)                             |OAuthの仕組みを利用して認証を実現する仕組み。歴史的な経緯でOIDCのことを単にOAuthと呼ぶ人も多い
認可サーバ                                       |OAuthにおいて、認可コードやOAuth Access tokenを発行するノード
リソースサーバ                                   |OAuthにおいて、実際の何らかのリソースを持つノード
クライアント                                     |OAuthにおいて、リソースサーバの情報を利用したいサービスの事

-

SAML/組織アカウント関連

用語                                             |意味
-------------------------------------------------|--------------------------
SAML(Security Assertion Markup Language)         |SSOを実現するための方針の1つ。特に理由がなければSAML2.0を指す
IdP(Identity provider)                           |SAMLにおける認証を実際に処理するノード
SP(Service Provider)                             |SAMLにおけるIdPに認証委任が可能なサービス
SCIM(System for Cross-domain Identity Management)|IDオートプロビジョニングを実現する方針規格の1つ
IDaaS(Identity as a Service)                     |IdPになりうるクラウドサービスの総称