# exercise-01. リリース・運用編
________________________________________
## 1. Logを集計せよ
________________________________________
### 課題

状況

```text
Webシステムがリリースされたが、不具合がいくつか発生している模様
アプリログ出力は実装されており、現在はINFO以上が2台のWebサーバ上それぞれのLogフォルダに出力されている
ログは100MBごとにローテーションしており、1日に8個程度のファイルすべて合わせて700MB程度である
あなたは、昨日上がったERROR以上のログを分類して集計することになった
リアルタイムに集計できる必要はないが、欲しくなったら15分以内に結果を提出できるようにしたい

・集計結果は、チーム内の開発メンバーで共有できれば良い
・一般的な開発PCに入っていそうなテキストエディタ・統合開発環境・Officeツール等を活用して良い
・Googleサービス、Slack、Skype等を活用して良い
```

ログ出力例

```text
2020-10-06 17:43:46,724 [7] [10021615] FATAL XX.Api.Server.Controllers.BaseController OutputErrorLog (0) - 呼び出し引数：{"cn01D00HeaderResultModel":null,"MtmrModel":{"ClientCdSek":"00067488", ...以下略... }
2020-10-06 17:43:46,724 [7] [10021615] FATAL XX.Api.Server.Controllers.BaseController OutputErrorLog (0) - システムエラーが発生しました。管理者にご連絡ください。（ユーザーID：10021615 要求元IP：10.100.0.110 呼び出しAPI名：CN01D00/ShitagakiSave）
System.Collections.Generic.KeyNotFoundException: 指定されたキーはディレクトリ内に存在しませんでした。
   場所 System.ThrowHelper.ThrowKeyNotFoundException()
   場所 System.Collections.Generic.Dictionary`2.get_Item(TKey key)
   …以下略
2020-10-06 18:45:01,763 [102] [10020883] INFO  XX.Core.Common.Interceptors.CustomInterceptor PreProceed (0) - 処理開始。(CO01D03Service.GetWebPageStartupMode)
2020-10-06 18:45:01,763 [102] [10020883] INFO  XX.Core.Server.Common.Data.DbConnection QueryForObject (0) - [GetConstVal] select                CM.CONST_MST_ID as "ConstMstId"              , CM.CONST_MST_NM as "ConstMstNm"              , CM.CONST_MST_VALUE as "ConstMstValue"          from                CONST_MST CM          where                CM.CONST_MST_ID =  '93'             and CM.DEL_FLG <> 1
2020-10-06 18:45:01,763 [108] [10021606] ERROR XX.Api.Server.Controllers.BaseController OnException (0) - 業務エラーが発生しました。(メッセージID：E2091, パラメータ：CN01D02.Schedule)
XX.Common.Exceptions.CustomException: 種類 'XX.Common.Exceptions.CustomException' の例外がスローされました。
   場所 XX.Core.Services.CN.CN01D00Service.CheckMtmrDataBeforeSave(CN01D00InitModel request, Boolean isApply, Boolean doWarnCheck)
   場所 XX.Core.Services.CN.CN01D00Service.SaveMtmrData(CN01D00InitModel model, Boolean isApply, Boolean doWarnCheck, Boolean isShitagakiFlg)
   場所 Castle.Proxies.Invocations.CN01D00Service_SaveMtmrData.InvokeMethodOnTarget()
   …以下略
```

ログのフォーマット要約

```text
日付時刻 [スレッドID] [ユーザID] エラーレベル  内容
例外メッセージ
   トレース内容
   トレース内容
   トレース内容
   …
```

### 問題

```text
1-1. ERROR以上を、「ユーザID エラーレベル 内容」で集計するとして、
　　何ができれば十分か？やるべき解決策や手順を考えてください
1-2. 抽出内容を「日付時刻 ユーザID エラーレベル 内容」
　　で抽出してください
1-3. 抽出内容を「日付時刻 ユーザID エラーレベル 内容 例外メッセージ トレース内容…」
　　で抽出してください

制限時間：6時間
```

### 解答例1-1

```text
顧客に提出する必要はないため、
抽出対象を抽出してExcelに貼り付けソートすれば十分
そのため、以下の手順で抽出を行う

※　なお正規表現のテスト自体は「正規表現 チェッカー」で
　　ググればいくらでもチェッカーサイトがあるので、
　　そこでテストすればよい

1. logファイル群をローカルにコピーする
2. Grepツールを使用して、ファイル群から対象行を抽出する
3. 抽出された内容を、正規表現で必要な情報だけに置換する
4. Excelに貼り付ける
5. Excel上でソートする

GREPツール・正規表現ツール例
sakura editor

GREPで使用する抽出条件例
[大文字・小文字区別][結果出力形式=結果のみ]
ERROR|FATAL

正規表現置換例
(^.*)(\[(\d{8})\]\s(((ERROR)|(INFO)|(FATAL))\s.*))
\3\t\4
```

### 解答例1-2

```text
1-1の正規表現置換を以下に変更

正規表現置換例
(^.*)(\d{4}\-\d{2}\-\d{2}\s\d{2}:\d{2}:\d{2}).*(\[(\d{8})\]\s(((ERROR)|(INFO)|(FATAL))\s.*))
\2\t\4\t\5
```

### 解答例1-3

```text
1-2のGREP条件を変更し、置換手順を追加変更

GREPで使用する抽出条件例
[大文字・小文字区別][結果出力形式=結果のみ]
ERROR|FATAL|^.*Exception\:|   場所 |パラメーター名\:

置換手順例
・いったんすべての\r\nを削除置換
・その後、以下で日付の手前に改行追加置換
(\d{4}\-\d{2}\-\d{2}\s\d{2}:\d{2}:\d{2})
\r\n\1
・その後、解答例1-2の通りに置換
```