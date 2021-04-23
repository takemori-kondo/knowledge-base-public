# front-01. Html, Css, JS
________________________________________
HTML5&CSS3標準デザイン講座 4刷  
https://www.shoeisha.co.jp/book/detail/9784798142265

とほほのWWW入門  
http://www.tohoho-web.com/www.htm

とほほのJavaScriptリファレンス イベントハンドラ  
http://www.tohoho-web.com/js/onevent.htm

改訂新版JavaScript本格入門 3刷  
http://gihyo.jp/book/2016/978-4-7741-8411-1  
(電子書籍は1刷。未改善正誤表あり)

Front-End Checklist  
https://github.com/thedaviddias/Front-End-Checklist

________________________________________
## 1. Terms, Practices, Syntax
________________________________________
### 1.1. Terms

Html/css terms

```text
Content models            : Flow contents(block level + inline level), phrasing contents(inline level)
Box model                 : content(width, height), padding, border, margin
Css selectors             : elements, .class, #id, :link, :visited, :hover, :active, :focus, !important
Css properties for layout : float:left/right, clear:both, position:absolute/fixed, display:block/inline/inline-block
Html5 section elements    : header, main, footer, nav, section
Html5 embedded elements   : audio, video, canvas
Html5 other features      : Block element in a, device-pixel-ratio
Css3 vendor prefixes      : -ms-, -webkit-, -moz-
Css3 flexbox layout       : display:flex
Css3 grid layout          : display:grid
Css3 attribute selectors  : E[att^="value"], E[att$="value"], E[att*="value"]
Css3 selectors            : input:enabled/disabled/checked, tr:nth-child(even)
Css3 media query          : -
Css3 other features       : text-shadow, rgba(r,g,b,a), border-radius, liner-gradient, transform
```

Design terms

```text
Reset css       : Eric meyer, Html5 doctor
Mobile approach : Mobile site, responsive, adaptive
Layout type     : 1column, multi-column, grid, free
Fluid layout    : Liquid/flexible, fluid grid
Responsive web  : Fluid + menu hide etc.
```

JS terms

```text
JS        : ES5.1/es2011(ie11 safe), es6/es2015
jQuery    : jQuery, jquery ui
JS eco    : npm (via node.js)
Framework : Augular4, react, vue3
```

Css practices

```text
大原則
1. インライン、styleタグは避け、外部ファイルのみで定義する
2. 全てclassに対してスタイルを定義する
3. 親子関係等は一切使わない
4. 詳細な作法はoocss（bootstrap）、bem、どちらかにする

Oocss
構造（box model）と構造に対する見た目（color, border-radius）を別classとして定義する
以下はBootstrap v4.2より
class="container" / "container-fluid"
class="row"
class="col-1" / "col-12"
class="table" / "table-bordered"
class="text-justify" / "text-left" / "text-center" / "text-right"
class="text-primary" / "text-secondary"
class="text-success" / "text-danger" / "text-warning" / "text-info"
class="bg-primary" / "bg-secondary"
class="bg-success" / "bg-danger" / "bg-warning" / "bg-info"
class="img-fluid" / "rounded img-thumbnail"
class="table" / "table table-dark"
class="table table-striped" / "table table-striped table-dark"
class="table table-bordered" / "table table-bordered table-dark"
class="table table-hover" / "table table-hover table-dark"
class="thead-light"
コンポーネントは以下の通り（一部省略）
alert, badge, breadcrumb, btn, btn-group, 
card, carousel slide, collapse, 
dropdown, form-control, input-group, modal, 
nav nav-tabs, navbar, pagination, 
spinner-border, spinner-grow, toast, tooltip

Bem
css名が構造ツリーの説明になる様にする
class="header"
class="main"
class="main__title-box"
class="main__title-box__title"
class="main__title-box__title--hover"
class="main__title-box__subtitle"
class="footer"

Sass/Scss
- cssから文法を拡張したもの
- js製トランスパイラだとnode-sass
```

Css processor/transpiler

```text
Transpiler/Altjs
- ES7/babel-core
- coffie script
- typescript

JS require()
- browerify
- require.js
- webpack
```

DOM構築と動作開始

```text
https://techblog.raccoon.ne.jp/archives/53180280.html

基本的な仕組み
1. 上から順番に読み込まれる
2. CSS解析完了=描画開始=javascript実行開始。DOM構築完了は考慮しない
3. 無修飾の外部jsとインラインjsは、DOM構築を中断する

例
1. CSS解析完了。インラインjsの実行 および 描画開始
2. DOM構築完了
3. defer属性外部ファイル実行
4. DOMContentloaded or jQuery.ready()
5. 画像読込完了
6. load
```

JS practices

```text
大原則
https://en.wikipedia.org/wiki/Unobtrusive_JavaScript
- インラインやbody内のscriptタグは避け、外部scriptのみで定義する（cssと同じ方針）
- つまりhtmlは、「共通script」、「そのhtml用script」の2種類を読み込む
- 「そのhtml用script」は、外部スクリプトではなく、htmlのbody一番下に記述することも許容する

控えめなdomスクリプティング
https://www.smashingmagazine.com/2008/09/jquery-examples-and-best-practices/
1. javascriptは機能層（behavior layer）*1として隔離して、htmlやcssから独立させる（大原則の言い換え）
2. デフォルトのテキストは、jsに頼らずhtmlとして記述する
3. htmlはセマンティック（＝タグの意味通りに使用する）マークアップする
4. 「そのhtml用script」は、onloadまたはjqueryのready関数（$(() => { ... })）
5. 初期状態で非表示だがUI操作後に表示するテキストは、cssで非表示にしておかず、jsで非表示にする*2

*1 2005年時点で言及あり
*2 2011年ごろから普及したスクロール/パララクスデザインはこの限りではないかも

Behavior layer(oocss version)
class="behavior-toggle-btn0001"
```

________________________________________
### 1.2. JavaScript 要点

use strictで防げること

1. varなし変数がグローバル化してしまうこと
2. 代入不可変数やプロパティに対して、代入が書けてしまうこと
3. 削除不可プロパティへ削除が書けてしまうこと
4. プロパティ名が重複宣言できてしまうこと
5. 引数名が重複宣言できてしまうこと
6. 8進数表記が使えてしまうこと

for問題

```text
for                : OK
for in             : 非推奨
for of             : 他言語のfoeachと等価
Array.forEach      : 非推奨
$.each(array, func): 非推奨
jQuery.each(func)  : まったく別用途
```

clickイベント問題

```text
イベントモデル
https://www.quirksmode.org/js/introevents.html
- Early eventhandlers (deprecated)            : <button onclick="doSomething()">Clap!</button>
- Traditional (deprecated )                   : element.onclick = () => { ... };
- Advanced w3c                                : element.addEventListener('click', () => { ... }, false)
- jQuery                                      : -
    1. jqObj.click(func)                      : 次の書き方のエイリアス
    2. jqObj.on('click', func)                : 個別に毎回バインドすればよければこれ
    3. $(document).on('click', selector, func): 事前に登録した定義で動的追加された要素に対しても反応したかったらこれ。ただし処理重め
```

JavaScript ES2011

- use strict
    - var必須
    - グローバル関数のthisがundifined化
    - eval()の厳密化
    - deleteの厳密化
    - いろいろな動作許容をエラーに変更
    - いろいろなプロパティを読み取り専用に変更
- 文法追加・削除　：予約語の変更
- 標準化・機能整理：Infinity, NaN, undifined
- 文法追加　　　　：\による複数行リテラル
- 文法追加　　　　：[]アクセス
- 標準化・機能整理：eval()
- 標準化・機能整理：typeof
- 標準化・機能整理：delete
- 標準化・機能整理：Arguments
- 文法追加　　　　：debugger
- 標準メンバー追加：Object
- 標準メンバー追加：Array
- 標準メンバー追加：JSON.stringify/parse
- 標準メンバー追加：bind()

JavaScript ES2015（=iPhone4s, iPad2, iPad miniは非対応）

- let, const（letならほぼC#同様の変数の挙動になる）
- Symbol型
- 8進数リテラル、2進数リテラル
- `${}`（テンプレートリテラル。複数行も可能）
- Map, WeakMap, Set, WeakSet
- Promiseパターン
- Proxyパターン
- 配列分割代入、オブジェクト分割代入
- for a of b
- thisが固定される() => ;
- thisが固定される() => { }
- デフォルト引数
- 可変長引数、可変長引数配列展開
- 引数名明示
- Class糖衣構文、static
    - メンバ変数を明示的に宣言しておくことはできない。constructorでお茶お濁す
    - アロー関数としてメンバ関数を定義できない
- moduleモード、import module
    - モジュールのimportはCORSの対象なので、ローカルファイルで実行すると遮断されてしまう
        - ※ ローカルファイル間は同一ドメイン扱いにならないため
- Iterator、yield(ジェネレーター)

________________________________________
## 2. Environment
________________________________________
### 2.1. Install Environment

```text
1. Chrome (by installer)
2. npm (by Node.js installer)
3. Visual Studio Code (by installer)
    - Beautify extension
    - ESLint extension
    - Debugger for Chrome extension

オススメの設定は # eco-01. Visual Studio Code を参照してください
```

________________________________________
### 2.2. Workspace 設定

パッケージ管理の有効化とeslintのダウンロード

```shell
# サンプルでは、front01～front05で別々にpackage管理するため、それぞれのフォルダの中に移動してから実行している
# eslintをグローバルインストールした場合は、settings.jsonにeslint.nodePathの設定が必要
npm init -y
npm install --save-dev eslint
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
- 実行をクリック
- launch.jsonを新しく構成する
    - 拡張で入れたChromeを選択
    - .vscode/launch.jsonが自動生成
- launch.jsonの詳細はサンプルソース参照のこと
```

________________________________________
### 2.3. Chrome自体でデバッグする場合の使い方

Chrome debug 操作(f12)

```text
Element > double click         : 一時編集
Element > [<-]                 : 画面から要素選択モード
Element > [[]]                 : デバイスエミュレータモード
Console                        : エラーの確認、任意のjs実行、
Sources                        : ブレークポイントの設置が可能
Sources > {}                   : ソースの整形表示可能
Sources > Consoles             : 任意のjsを実行可能
Network                        : Ajaxなどのリクエストとレスポンスを確認可能
Application > Storage > Cookie : Cookieなどを確認可能
```

________________________________________
## 3. Samples
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
- FrontSamples/front01/js/javascript-hello-world.js