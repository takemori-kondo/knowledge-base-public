# eco-08. OWASP ZAP
________________________________________
体系的に学ぶ 安全なWebアプリケーションの作り方 第2版 2刷  
https://www.sbcr.jp/products/4797393163.html  
(電子書籍は1刷。未改善正誤表あり)

________________________________________
## 1. Environment
________________________________________
### 1.1. Version

- Windows10 Pro 1089 build17763
- Java Runtime Environment(JRE) 64bit 8-
- OWASP ZAP
- Fire Fox
- Foxy proxy(fire fox add-on)

________________________________________
### 1.2. Install

1. Install 64bit jre8
2. Download & install owasp zap
3. Install firefox
4. Add foxy proxy standard add-on to firefox

※ Foxy proxy standard はなくても良い。その場合は手動でプロキシを設定する

________________________________________
## 2. Settings
________________________________________
Owasp zapの設定

```text
1. 起動時：現在のタイムスタンプでファイル名を付けてセッションを保存
2. ツール > オプション
    - ローカル・プロキシ : localhost, 58888
    - ブレークポイント : ブレークボタンモード：リクエストとレスポンスのボタンを個別表示
    - 表示 > フォント名 : メイリオ
```

Foxy proxy standardの設定

```text
いい感じに設定して、検証したいサイトだけowasp zap経由でプロキシされるようにする
※　FireFoxがバージョンアップすると、Foxy Proxyが機能しなくなる場合がある
　　その場合は、Foxy proxyを一度削除して再インストールすればよい
```