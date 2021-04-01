# eco-09. curl
________________________________________
## 1. curl
________________________________________
### 1.1. Windows PowerShellでcurlを使う方法

%HOMEPATH%\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1

```text
del alias:curl
```



________________________________________
### 1.2. curlサンプル

httpリクエストのサンプル

```curl
curl --http1.0 --get --data-urlencode "search word" http://localhost:18888
```

```curl
curl --http1.0 -H "X-Test: Hello" http://localhost:18888
```

```curl
curl -L http://localhost:18888
```

```curl
curl -d "{\"hello\": \"world\"}" -H "Content-Type: application/json" http://localhost:18888
```

```curl
curl -d @test.json -H "Content-Type: application/json" http://localhost:18888
```

```curl
curl -X GET --data "hello=world" http://localhost:18888
```

```curl
curl --http1.0 -d title="The Art of Community" -d author="Jono Bacon" http://localhost:18888
```

```text
<form method="POST">
    <input name="title">
    <input name="author">
    <input type="submit">
</form>

// curl
curl --http1.0 --data-urlencode title="Head First PHP & MySQL" --data-urlencode author="Lynn Beighley, Michael Morrison" http://localhost:18888

// encorded
title=Head%20First%20PHP%20%26%20MySQL&author=Lynn%20Beighley%2C%20Michael%20Morrison
```

```html
<form action="POST" enctype="multipart/form-data">
    <input name="attachment-file" type="file">
</form>

// curl
curl --http1.0 -F title="The Art of Community" -F author="Jono Bacon" -F attachment-file=@test.txt http://localhost:18888
```