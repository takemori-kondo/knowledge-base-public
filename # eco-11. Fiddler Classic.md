# eco-11. Fiddler Classic
________________________________________
Fiddlerの使い方 - IPA 独立行政法人 情報処理推進機構  
https://www.ipa.go.jp/files/000077215.pdf

※ リクエストキャプチャツールは好みの問題なので、WiresharkでもFiddlerでも好きなのを使えばよい
________________________________________
## 1. Prepare
________________________________________
Install

1. download & Install Fiddler
2. 「WindowsのAppContainerテクノロジのせいで、Edgeなどをキャプチャしようとしても妨害されるかも。ツールバーのWinConfigボタンを使って調整してね。今後このメッセージ表示しない場合はCancel押してね」Cancel

典型的な設定

1. ステータスバー一番左、Cupturing は ONにする
2. ステータスバー左から二番目、表示内容は Web Browser
3. Tools > Option > Connectionタブ > Act as system proxy on startupにチェックが付いているか確認

その他、Windows8.1以降ではうまく動かない場合あり

1. WinConfigボタンで色々調整の事

________________________________________
## 2. How To Use
________________________________________
ng serve したAngularをChromeで操作して、そのサイトのAPIサーバへのリクエストだけキャプチャしたい場合

```text
前提
1. Angularは以下でホストしている
localhost:4200
2. APIサーバは以下でホストしている
localhost:64438
3. AngularからAPIサーバへのリクエスト（=XHR）だけキャプチャしたい

やり方
1. 右側ペインのFilstersを開く
2. Request Headers欄 > Show only if URL containsに、localhost:64438
3. Fiddlerを再起動
```

その他操作

```text
履歴のクリア : ツールバー の× > RemoveAll
```

________________________________________
## 3. Httpsリクエストの中身を見る
________________________________________
やり方

```text
前提
自社サイトなど、証明書のprivateキーがあること

やり方
調整中
```