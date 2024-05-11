# eco-09. curl
________________________________________
## 1. curl
________________________________________
### 1.1. Windows PowerShellでcurlを使う方法

%HOMEPATH%\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1

```text
# 初期状態では、Invoke-WebRequestのエイリアスになってしまっている（curl.exe自体は別途標準で存在するのに）
# それを今のコンソールで一時的に解除するのがこのコマンド
# あるいはcurl.exeとすればエイリアスが呼ばれずcurl.exeを呼べる
del alias:curl
echo "del alias:curlが実行されました"
```

Windows版curl.exeの注意点

```text
コンソール上で指定した文字列内の"は常にエスケープされてしまう
文字列がファイルから読み取られる場合はこの問題は起きない
よって以下のどちらかで解決すると良い
・方法A 一度ファイルに記載してそれを読む
・方法B .Replace('"', '""')
```

________________________________________
### 1.2. 主に使用する引数

|引数  |意味
|------|-----------------------------------------------------
|--help|ヘルプを表示
|-X    |httpメソッド。別表記で-XGET, -XPOSTの記法も許容される
|-H    |ヘッダ
|-d    |POSTやPUTなどの、通常時のdata。             name=value。@でファイル指定可能
|-F    |POSTやPUTなどの、multipart/form-dataのdata。name=value。@でファイル指定可能
|-i    |レスポンスヘッダも出力する（ない場合はボディしか出力されない）
|-o    |レスポンスをファイルに出力（-iと組み合わせればヘッダも出力される）
|-O    |リクエスト対象がファイルである時、そのままの名前で保存
|-sS   |進捗は表示しないが、エラーがあったら表示する
|-v    |verbose（詳細を出力）

-

________________________________________
### 1.3. curlサンプル

サンプル

```powershell
curl -X 'GET'  'https://localhost:7146/v1/WeatherForecast?P1_Bool=true'
curl -X 'GET'  'https://localhost:7146/v1/WeatherForecast?P1_Bool=true' -o 'C:\curl\response.txt' -i -v
curl -X 'POST' 'https://localhost:7146/v1/WeatherForecast'              -H 'Content-Type: application/json' -d '{ "p1_Bool": true, "p2_Int": 0, "p3_String": "string", "p4_Double": 0 }'.Replace('"', '""')
curl -X 'POST' 'https://localhost:7146/v1/WeatherForecast'              -H 'Content-Type: application/json' -d @C:\curl\requestdata_json.txt
curl -X 'PUT'  'https://localhost:7146/v1/WeatherForecast'              -H 'Content-Type: multipart/form-data' -F 'option=aaa' -F 'file=@C:\curl\sample_file.png'
curl -XGET  "https://slack.com/api/auth.test" -H "Authorization: Bearer xoxb-xxxxxxxxxxxx-xxxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxxx"
curl -XPOST "https://slack.com/api/auth.test" -d "token=xoxb-xxxxxxxxxxxx-xxxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxxx"
```

サンプルの補足

```text
POST等の-dや-Fは、form > inputタグのname=入力された値に対応する
multipart/form-dataでも同様
なお2022年現在、Box APIやSlack APIなどのファイルアップロード系APIはAPIだがmultipart/form-data
```