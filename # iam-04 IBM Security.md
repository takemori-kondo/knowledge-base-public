# iam-04 IBM Security
________________________________________
IBM Securityサービスシリーズの概要

最新名称              |SAMLやLDAP役割|旧名
----------------------|--------------|----------------------------------------
Verify Access         |IdP           |Access Manager(ISAM)
Verify Governance     |IdP           |Identity Manager(ISIM), IGI
Directory Server(ISDS)|LDAP          |-
SaaS                  |-             |-

.

```txt
[User Browser]
    ↓ ログイン要求（SAML）
[SAML SP (開発アプリ)]
    ↓ リダイレクト
[ISAM (IdP)]
    → ISDS にパスワード照会（LDAP認証）（予めISIMからISDSに属性が同期済のケースが多し）
    ↓ SAMLアサーションを返す
[SAML SP (開発アプリ)] SAMLアサーション受け取り・ログイン成功
```