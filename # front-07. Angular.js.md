# front-07. Angular.js
________________________________________
## 1. Prepare
________________________________________
Install Git

http://git-scm.com/download/win

Download by npm

```shell
# nodeとバージョンが食い違うと動かないので、本当はバージョン指定を推奨
npm install -g @angular/cli
```

Initialize project folder

```shell
git --version
ng new front07
```

Transpile & Run by localhost:4200

```shell
cd front07
ng serve

# ** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **
# と出れば、成功。上記URLでブラウザからアクセスできる
```

典型的なバージョン例（やや古め）

```shell
node -v
#v10.12.0
npm -v
# npm 6.4.1
ng version
# Angular CLI: 7.3.6
```

便利なコマンド

```shell
# これらのコマンドは、通常はapp.module.tsがある場所で行う
ng generate component ...
ng generate service ...
ng generate module app-routing --flat --module=app
```

________________________________________
## 2. 要約
________________________________________
構造の要約

1. vueと考え方や作法は同じ
2. styles.cssが全体のスタイル設定
3. app/app-routing.module.ts が ページ（≒コンポーネント）やURLルーティングを司る
    - SPAでも、考え方や書き方は普通のViewとほぼ変わらない
    - 初期ページは例えば以下
        - { path: '', redirectTo: '/dashboard', pathMatch: 'full' },
4. app/app.module.ts が 全体のimport解決を司る
5. 自作コンポーネントについては以下
    - css、html、tsの3つでワンセット(ng generateだとspecファイル（≒自動テストのひな型）も作られる）
    - 自作コンポーネントのファイル名は慣習的に、コンポーネント名.component.xxx
    - 自作コンポーネントのセレクタは慣習的に、app-コンポーネント名
    - 自作コンポーネントは、app/app.module.tsに追加する
        - import { ProductAlertsComponent } from './product-alerts/product-alerts.component';
        - declarations: [..., ProductAlertsComponent]
    - ng generateなら上記に従ってひな形を用意・app/app.module.tsの書き換えをしてくれる
6. 標準コンポーネントについては以下
    - 標準コンポーネントも、app/app.module.tsに追加する
        - import { FormsModule } from '@angular/forms';
        - imports: [..., FormsModule]
7. 作法として、componentはViewの役割に徹する
8. ロジック（ドメイン・Model・POtsO・Service）について
    - 本来はフォルダを切って整理するべきか（サンプルではappフォルダ直下においてたが…）
    - AngularはDIコンテナの仕組みに対応している。constructorの引数は勝手にDIされる
    - データ取得系のServiceは非同期対応するためJsRxを前提にする
9 app.component.htmlには以下が定番
    - ヘッダエリア
    - <nav>と routerLink（メニュー）
    - <router-outlet></router-outlet>（非共通のメインページ部分）
    - フッタエリア
    - メッセージ

標準クラス

```text
#@angular/core
Component
OnInit
Input
Output
EventEmitter
Injectable

#@angular/forms
FormsModule
```

テンプレート構文の要約

```html
<!--
テンプレートFor         :*ngFor="let item of list"
outバインド             :[html-attrまたはts-property]="ts-expression"
イベントバインド        :(html-attrまたはts-property)="ts-function-call"
双方向バインド          :[(html-attrまたはts-property)]="ts-function-call"
補間バインディング      :{{ts-expression}}
補間バインディングパイプ:{{ts-expression | pipe-function}}
テンプレートIf          :*ngIf="ts-expression"
Observable              :<li *ngFor="let hero of heroes$ | async" >
-->
<div *ngFor="let product of products">
    <h3>
        <a [title]="product.name + ' details'">
            {{ product.name }}
        </a>
    </h3>
    <p *ngIf="product.description">
        Description: {{ product.description }}
    </p>
    <button (click)="share()">
        Share
    </button>
    <app-product-alerts
        [product]="product"
        (notify)="onNotify()">
    </app-product-alerts>
</div>
```

TypeScript補足

```text
    // https://js.studio-kingdom.com/typescript/handbook/interfaces

    // @Foo()はデコレータ。他の言語のアノテーション、属性クラスに相当
    // ?は任意プロパティ（いなくてもよい）
    // : Typeが型指定
    
    @Input() hero?: Hero;
    
    // readonly
    // `${foo}`は変数展開される文字列
    // $接尾辞でObservable
```

JsRx補足

```text
of()   : 同期データをObservableでラップする
pipe() : パイプする
tap()  : ログなどを実現するために使う（受け取った値そのものには何もしない）
```