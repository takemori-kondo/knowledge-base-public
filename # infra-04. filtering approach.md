# infra-04. filtering approach
________________________________________
## 1. 前提知識
________________________________________
### 1.1. DNS, Http, SSL/TLSと通信手順

1. クライアントが、DNS解決を行う
2. IPを使用してhttpリクエスト※
3. サーバが応答

※ Httpリクエスト内にはドメイン情報もある。言い換えるとこの中にしかドメイン情報はない

________________________________________
### 1.2. SSL/TLSが暗号化する範囲、しない範囲

暗号化  |情報
--------|-------------------------------
されない|自端末IP/ポート
されない|宛先IP/ポート
される  |宛先URL
される  |HTTPリクエストやレスポンスそのもの(Webコンテンツ全般含む)

※ 要約すると、L4以下は暗号化されない

※ 言い換えると、SSL/TLSはL5～6辺りの位置にある扱い

いつまで鍵が有効かという概念で見ればL5に含まれ、データをどう表現したか（暗号化したか）という観点ならL6

________________________________________
## 2. L7プロキシ
________________________________________
L7プロキシの仲間

概念            |意味
----------------|-----------------------
リバースプロキシ|汎用的なリバースプロキシ。AWS ALBなどは事実上LBよりこっちに近い
L7-LB           |L7ロードバランサなノード。SSL復号済みまたはここで複合する必要がある
WAF             |攻撃防御に特化したノード。FQDNフィルタリングなど

※ L7プロキシはSSL/TLS復号済みか、それ自身が復号処理しないと機能しない

________________________________________
## 3. やりたいこと、手法
________________________________________
### 3.1.受信に関するアプローチ

やりたいこと   |レイヤ|アプローチ
---------------|------|---------------------------
FQDN制限       |L7    |LB、リバースプロキシ、クラウド付属のWAF、Webサーバ自身の機能
IP制限         |L3    |クラウドのWAF、firewalldなどでフィルタリング

________________________________________
### 3.2. 送信に関するアプローチ

やりたいこと   |レイヤ|アプローチ
---------------|------|---------------------------
DNS解決制限    |L7    |LB、リバースプロキシ、クラウド付属のWAFなど