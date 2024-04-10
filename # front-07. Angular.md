# front-07. Angular
________________________________________
Angular 日本語ドキュメンテーション  
https://angular.jp/

Angular Example - Template Syntax Collection - StackBlitz  
https://stackblitz.com/angular/ymrdylabkpo?file=src%2Fapp%2Fapp.component.html

Angular 日本語ドキュメンテーション - コンポーネントの相互作用  
https://angular.jp/guide/component-interaction

Angular 日本語ドキュメンテーション - ライフサイクルフック  
https://angular.jp/guide/lifecycle-hooks

Angular 日本語ドキュメンテーション - リアクティブフォーム  
https://angular.jp/guide/reactive-forms

Angular 日本語ドキュメンテーション - Attribute directives  
https://angular.jp/guide/attribute-directives

Angular 日本語ドキュメンテーション - ルートモジュールによるアプリケーションの起動  
https://angular.jp/guide/bootstrapping

Angular 日本語ドキュメンテーション - ユーザー入力  
https://angular.jp/guide/user-input

________________________________________
## 1. 特徴

特徴

- typescript
- scss
- ng serve
- 独自タグ、独自属性を作る構文
- SPAルーティング

要約

1. html、scss、tsの3つワンセットで独自タグになり、コンポーネントと呼ぶ
    - scssはそのコンポーネントのみに影響する。子コンポーネント等に影響しない
    - テンプレートにおいてSPAルーティング用リンクは、linkの代わりにrouterlinkを使用する
    - テンプレートにおいてangularに管理されたイベントは、onclickなどの代わりに(click)を使用する
    - その他、各種テンプレート構文が使用できる
2. 以下がルートコンポーネント
    - src/styles.scss
    - src/app/app.component.html
    - src/app/app.component.ts
3. app/app-routing.module.ts がSPAルーティング
4. app/app.module.ts が ルートngモジュール
    - ngモジュールに関しては後述
5. 付属のライブラリや機能
    - NgModelまたはReactive Form
    - HttpClient
    - ルーティングガード（CanActivate、CanDeactivate）
    - JsRxヘルパー（of()、pipe()、tap()）
6. DIコンテナあり

________________________________________
## 2. 準備
________________________________________
### 2.1. 環境用意

Is there a compatibility list for Angular / Angular-CLI and Node.js?  
https://stackoverflow.com/questions/60248452/is-there-a-compatibility-list-for-angular-angular-cli-and-node-js

```text
1. Git
2. Node.js
   Angularのバージョンによって必要なNode.jsのバージョンが異なる
   ver.7以降に全対応するには、以下の3バージョンがあればよい
    - Angular  7～11 : Node.js ^10.13.0
    - Angular 12～15 : Node.js ^14.20.0
    - Angular 16～   : Node.js ^18.10.0（2023年4月現在、正式リリースはまだ）
3. Angular CLI(例、7.3.6をインストールする場合)
    - npm install -g @angular/cli@7.3.6

* Node.jsのバージョンを複数インストールする場合は、nvm（mac等の場合）またはnvm-windows（Windowsの場合）活用する必要あり。
  # eco-05ex1. Node.js も参照のこと
* Node.jsを入れる過程で「自動的に...」をチェックしておくと、ChocolateyやPython3が入る
* 既存のワークスペースならワークスペース内にngがあるため、npx ng …での実行も可能
  自前である程度いくらかのコードの追加や設定を済ませた俺俺テンプレートを基にするなら、ng newする必要はなくなるためおすすめ
  ng new で新規プロジェクトを作る場合はグローバルにダウンロードされたngが必要
* Googleに匿名でデータ送っていい？と毎回聞かれるのが面倒な場合、環境変数にNG_CLI_ANALYTICSをfalseで追加すると聞かれなくなる
```

________________________________________
### 2.2. Angular CLIのヘルパーコマンド

新規プロジェクト作成と実行

```powershell
git --version
ng new front07
# ? Would you like to add Angular routing? Yes
# ? Which stylesheet format would you like to use? SCSS
cd front07
npx ng serve
# ** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **
# と出れば、成功。上記URLでブラウザからアクセスできる
# 以降、vscodeで作業する場合はfront07をルートフォルダとして開くとよい
```

便利なコマンド

```powershell
# これらのコマンドは、通常はapp.module.tsがある場所で行う
npx ng generate component features/todo/todo-index --skip-tests=true
npx ng generate directive ...
npx ng generate service ...
```

________________________________________
### 2.3. VSCode標準機能によるTypescriptデバッグ

```text
ver 1.60.1で可能

Angularプロジェクトのフォルダをルートフォルダとして開きなおす
実行 > 構成の追加 > chrome
npx ng serve
実行 > デバッグ実行
```

________________________________________
### 2.4. 実行中の内容にDebug Consoleで介入

例：あるコンポーネントの変数を書き換えて画面に反映したい場合

```text
// Angular9～
// それ以前ではこの方法はできない
// 
// 例えば以下の時
// - app-todo-indexタグがある
// - そのコンポーネントにtodos配列プロパティがある
// - todoのメンバは、id、text

// 1. 対象のコンポーネントインスタンスを取得
let cmp = ng.getComponent(document.getElementsByTagName('app-todo-index')[0]);

// 2. 変数を自由に書き換える
cmp.todos[0].text = 'コンソールから書き換えました！';

// 3. Mを変更したのでVに通知（コンソールから直接書き換えたため、自動で通知が飛ばない）
ng.applyChanges(cmp);
```
________________________________________
## 3. サンプル
________________________________________
front07参照のこと