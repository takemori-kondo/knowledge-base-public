# front-01. Html, Css, JS
________________________________________
## 1. Environment
________________________________________
### 1.1. Install Environment

```text
1. Chrome (by installer)
2. npm (by Node.js installer)
3. Visual Studio Code (by installer)
    - ESLint extension

※ かつてvscodeの拡張機能にBeautify、Debugger for Chromeが主流でしたが、標準対応したため不要になりました

オススメの設定は # eco-01. Visual Studio Code を参照してください
```

________________________________________
### 1.2. Workspace 設定

パッケージ管理の有効化とeslintのダウンロード

```shell
# サンプルでは、front01～front05で別々にpackage管理するため、それぞれのフォルダの中に移動してから実行している
# eslintをグローバルインストールした場合は、settings.jsonにeslint.nodePathの設定が必要
npm init -y
npm install --save-dev eslint
npm install --save-dev eslint-plugin-html
npx eslint --init
    ? How would you like to use ESLint? (Use arrow keys)
        > To check syntax and find problems
    ? What type of modules does your project use? (Use arrow keys)
        > None of these
    ? Which framework does your project use? (Use arrow keys)
        > None of these
    ? Does your project use TypeScript? (y/N)
        > n
    ? Where does your code run? (Press <space> to select, <a> to toggle all, <i> to invert selection)
        > (*) Browser
    ? What format do you want your config file to be in? (Use arrow keys)
        > JavaScript

# eslintでチェックを実行してみる
npx eslint .\js\javascript-hello-world.js
```

Chromeのデバッグ機能をVSCodeに紐づける

```text
ver 1.60.1以降、拡張なしでJavascriptやTypeScriptのデバッグ実行が可能になった
実行 > 構成の追加 > chrome
以降、5Fでデバッガと紐づいた状態のChromeで表示できる
.vscode/launch.jsonの典型的な設定は、ソースコードに添付されているため参照の事
```

________________________________________
### 1.3. Chrome自体でデバッグする場合の使い方

Chrome debug 操作(f12)

```text
Element > double click         : 一時編集
Element > [<-]                 : 画面から要素選択モード
Element > [[]]                 : デバイスエミュレータモード
Console                        : エラーの確認、任意のjs実行。※下のペインのConsoleも同様
Sources                        : ブレークポイントの設置が可能
Sources > {}                   : ソースの整形表示可能
Network                        : Ajaxなどのリクエストとレスポンスを確認可能
Application > Storage > Cookie : Cookieなどを確認可能
```

________________________________________
### 1.4. その他の典型的なWorkspace初期設定

npm & webpack & scss(sass)

```powershell
npm init -y
npm install node-sass --save-dev --no-bin-links
npm install webpack webpack-cli --save-dev --no-bin-links
```

※ ある程度複雑なWebフロントエンドを作るなら、3大farameworkの導入を検討すること

________________________________________
## 2. Samples
________________________________________
Index

- FrontSamples/index.html

launch.json

- FrontSamples/.vscode/launch.json

HTML5の基本

- FrontSamples/front01/html-sample.html
- FrontSamples/front01/css/html-sample.css

JS(ES6)のサンプル

- FrontSamples/front01/javascript-hello-world.html
- FrontSamples/front01/js/javascript-sample01-hello-world.js
- FrontSamples/front01/js/javascript-sample02-strict-mode.js
- FrontSamples/front01/js/javascript-sample03-es2011.js
- FrontSamples/front01/js/javascript-sample04-es2015.js
- FrontSamples/front01/js/javascript-sample05-es2018.js
- FrontSamples/front01/js/javascript-sample06-practice1-for.js
- FrontSamples/front01/js/javascript-sample07-practice2-event.js

Cookieのサンプル

- FrontSamples/front01/cookie-sample.html
- FrontSamples/front01/subdir/cookie-sample.html