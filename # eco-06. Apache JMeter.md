# eco-06. Apache JMeter
________________________________________
## 1. Environment
________________________________________
### 1.1. Version

- Windows10 Pro 1709
- Java Runtime Environment(JRE) 8-
- Apache JMeter 4.0

________________________________________
### 1.2. Install

1. Install JRE8
2. Download & unzip apache-jmeter

________________________________________
## 2. Settings
________________________________________
### 2.1. Frequency Settings

Test Plan

```text
各スレッドグループを別々に実行 = true
```

Thread group

```text
Threads : Number of parallel requests
Ramp-Up : Number of seconds until all threads start
Loop    : Number of loop
```

HTTP Cookie Manager

```text
繰り返しごとにクッキーを破棄しますか？ = false
```

________________________________________
### 2.2. Login

```text
User Defined Variables : needsLogin
If Controller          : Expression = ${needsLogin}
Regex Extractor        : csrf_token
                         <input type="hidden" name="_token" value="(.*?)">
                         $1$
BeanShell              ：vars.put("needsLogin", "false");
```

### 2.3. Auth for Azure

```text
1. Auth by browser.
2. Note header (authorization: bearer{access_token}).
3. Use it.
```