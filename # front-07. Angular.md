# front-07. Angular
________________________________________
TypeScript 日本語ハンドブック | js STUDIO  
https://js.studio-kingdom.com/typescript/

Angular 日本語ドキュメンテーション  
https://angular.jp/

Is there a compatibility list for Angular / Angular-CLI and Node.js?  
https://stackoverflow.com/questions/60248452/is-there-a-compatibility-list-for-angular-angular-cli-and-node-js

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

________________________________________
## 0. 特徴

特徴

- typescript前提
- scss組み込み済み
- 開発用のローカルサーバ立ち上げ機能内蔵（ng serve）
- Vueと異なりMVVMではなくMVC
    - デバッグコンソールでコンポーネントの状態を直接変更した時など、Vへの反映にはapplyChange()が必要

Vueから先に学んだほうが良い？

1. VueとAngularのテンプレート構文、コンポーネント、ディレクティブの考え方はほぼ一緒
2. Vue CLIとAngular CLIができることも大体同じ
3. VueはTypeScriptを強要されない
4. Angularを学ぶ前に、Vueを学んだほうがステップアップとしてはわかりやすい
5. VueからAngularに乗り換えるタイミングは以下のいずれかを考えるようになったら
    - TypeScriptを普段から使うが、Vue CLIで新規プロジェクトを初期化した後の調整が面倒だ
    - Scssを普段から使うが、Vue CLIで新規プロジェクトを初期化した後の調整が面倒だ
    - DIコンテナが欲しい、自分で組み込むのは面倒だ
    - 大規模開発に従ったルールでフォルダ分け・コンポーネント分け・DIしたい
    - とても複雑なフォームバリデーションが必要
    - DOMでコンポーネントを入れ子にして、複雑に投影を設定したい

________________________________________
## 1. 準備
________________________________________
### 1.1. 環境用意

```text
1. Git
2. Node.js(Angularのバージョンにあったバージョンが必要)
    - Angular  7.x~ 8.x : Node.js 10.x(10.9~)
    - Angular  9.x~11.x : Node.js 12.x(12.11~)
    - Angular 12.x~13.x : Node.js 14.x(14.15~)
3. Angular CLI(例、7.3.6をインストールする場合)
    - npm install -g @angular/cli@7.3.6
```

________________________________________
### 1.2. Angular CLIのヘルパーコマンド

新規プロジェクト作成と実行

```powershell
git --version
ng new front07
cd front07
ng serve
# ** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **
# と出れば、成功。上記URLでブラウザからアクセスできる
```

便利なコマンド

```powershell
# これらのコマンドは、通常はapp.module.tsがある場所で行う
ng generate component pages/todo/todo-index
ng generate directive ...
ng generate service ...
```

________________________________________
### 1.3. VSCode標準機能によるTypescriptデバッグ

```text
ver 1.60.1で可能
実行 > 構成の追加 > chrome
ng serve
実行 > デバッグ実行
```

________________________________________
## 2. Typescript
________________________________________
### 2.1. Typescript要約

サンプルコード

```ts
@Component({
  selector: 'app-todo-index',
  templateUrl: './todo-index.component.html',
  styleUrls: ['./todo-index.component.css']
})
export class TodoIndexComponent implements OnInit { constructor(

  private formBuilder: FormBuilder,) { }

  public addAreaForm: FormGroup = this.buildForm();
  public todos: any[] = [];

  public ngOnInit(): void {
    // this.todos = this.getTodos();
  }
}
```

基本事項（C#との比較）

1. @は属性（tsではデコレータと呼ぶ）
2. jsonリテラルが可能（javascriptの機能）
3. 末尾カンマはあらゆるところで許容される
4. constructor引数にアクセス修飾子で、メンバ宣言不要&代入
5. アクセス修飾子のデフォルトはpublic。省略は非推奨
6. any型は避けるべき
7. C#のvar型はunknown
8. importとexportは作法に従うこと（vscodeで予測入力利きます）
9. htmlイベントハンドラにはコードブロックの中身だけ渡す（javascriptの考え方）
    - 識別子やラムダ式を渡すわけではない
    - イベントが渡す引数名は必ずevent
        - ※Angular独自の仕組みである(click)などは$eventとなる
    - foo(event);
    - foo(event); alert('!!!!');

TypeScriptでの型検査

- typeof a : objectの詳細検査はできない
- a instanceof(T) : true/falseを返す。interfaceに対しては使えない
- ユーザ定義ガード : interfaceに対して動的チェックしたい場合

Typescript特有の考え方

1. 構造的な型チェック（継承や実装を宣言しなくてよい）
    - 宣言ではなく実際の型構造で静的検証する
    - is aではなくlooks like a
    - ダックタイピング
2. オプションプロパティ「public a?: int」
    - そのメンバがいなくてもよい
    - 他言語のnullableではない

$の付く識別子

- 言語仕様としては特別な意味はない
- Chromeのデバッガは、Elementsタブで選択したElementは$0に入っている
- jsフレームワークは、たいていの場合特別な意味を持たせる慣習がある
    - jQueryの場合、$(str)関数があり、これが根源である
    - Angularの場合
        - Angularイベントの引数名は$eventで固定
- Observableに$接尾辞をつける慣習

________________________________________
### 2.2. その他

tsconfig.json の推奨

- strict
    - alwaysStrict
    - noImplicitAny
    - noImplicitThis
    - strictNullChecks
- noEmitOnError
- noFallthroughCasesInSwitch
- noImplicitReturns
- noUnusedLocals
- noUnusedParameters
- strictBindCallApply
- strictFunctionTypes
- strictPropertyInitialization

tslint.json版

- 保留

________________________________________
## 3. Angular
________________________________________
### 3.0. 実行中の内容にDebug Consoleで介入

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
### 3.1. テンプレート構文のサンプル

サンプルコード

```html
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

テンプレート構文要約

1. 補完（Interpolation）
    - TypeScriptの結果を表示
        - {{ expression }}
        - 属性="{{ expression }}"
2. 式コンテキスト（Expression context）
    - 左辺が[]や*ngの場合、右辺にはTypeScript式を書く
        - [属性]="expression"
        - *ngFor="let todo of todos"
3. 文コンテキスト（Statement context）
    - 左辺が()の場合、右辺には複数のTypeScript文が可能
    - (独自イベント)="foo(); bar();"
    - (独自イベント)="foo($event); bar($event);"
        - イベントから渡されるイベント引数名は、$eventでなければならない
        - $eventは、主にstopPropagation()などするために使う
4. 独自タグ（New Mental Model）
    - コンポーネントのこと
        - [@Inputなproperty]="expression"
        - [@Inputかつ@Outputなproperty]="property"
        - (@OutputなEventEmitter property)="foo($event); bar($event);"
    - 組み込み済みコンポーネント
        - router-outlet : ルーティングされるページの置き換え位置
        - ng-content : componentが子タグを受け入れれる際の置き換え位置
        - ng-template と ng-container : 条件付きで表示する場合に使用。かなり冗長
5. 独自属性（New Mental Model）
    - ディレクティブのこと
        - [@Inputなproperty]="expression"
        - [@Inputかつ@Outputなproperty]="property"
        - (@OutputなEventEmitter property)="foo(); bar();"
        - selector名は必ず[]で囲まれなければならない
        - selector名とproperty名を同名にすると、利用側はselector名を省略可能
    - 組み込み済みディレクティブ
        - onclick → (click) など、htmlイベント全般
        - class → [ngClass] : 配列やkey-boolなjsonで指定することが可能
        - style → [ngStyle] : 配列やkey-stringなjsonで指定することが可能
        - [(ngModel)]="property" : 主にinput要素に対して双方向バインドを実現する
        - [attr.属性] = "expression"
        - [class.クラス名] = "bool expression"
        - [style.CSSプロパティ] = "expression"
    - 組み込み済みディレクティブ（構造ディレクティブ）
        - *ngIf="expression"
        - *ngFor="let todo of todos"
        - *ngFor="let todo of todos; let i = index"
        - *ngFor="let todo of todos; let i = index; trackBy: hoge"
        - [ngSwitch]と*ngSwitchCase
6. 属性とプロパティに関する補足（Property vs. Attribute）
    - 要点
        - 同時に指定すると、プロパティが勝つ
        - 左辺がhtml属性の場合、右辺はTypescriptは無理
        - 左辺が()や[]の場合、右辺はTypescript式でなければならない
        - onclick属性などは使わず、(click)を使用すること
    - 例：disabledと[disabled]
        - disabled="false"
            - disabled属性はそもそも="..."する属性ではない（="false"は無視される）
        - [disabled]="false"
            - disabledプロパティに対してfalseを設定するため合法で、有効になる
7. 別の書き方
    - bind-xxxは[xxx]の別の書き方。同じ意味
    - on-xxxは(xxx)の別の書き方。同じ意味
    - bindon-xxxは[(xxx)]の別の書き方。同じ意味
8. [属性]や(独自イベント)の意味（Property Binding、Event Binding）
    - プロパティを操作したり、イベントにバインドする
        - [属性や独自属性]="expression"
        - (独自イベント)="foo($event); bar($event);"
9. [()]による双方向バインディング（Two-way Binding）
    - @Inputかつ@Outputなプロパティ
10. タグ変数（Template reference variables）
    - #thisTag
        - 設定したタグそのものが入った変数になる(HTMLElement型)
        - 表示更新には別途()によるイベントの発生が必要
        - 例：(keyup)="0"
11. パイプ（Pipe）
    - {{ title | uppercase }}
12. Null条件演算子（Safe navigation operator）
    - {{ foo?.name }}
13. anyにキャスト
    - {{ $any(foo).name }}
14. Observable
    - "*ngFor="let hero of heroes$ | async"

________________________________________
### 3.2. 要約

全体像

1. html側はvueと考え方は同じ
2. 全体画面は以下
   - src/styles.scss(または.css)
   - src/app/app.component.html
   - src/app/app.component.ts
3. app/app-routing.module.ts がURLルーティングを司る
4. app/app.module.ts が 全体のimport解決を司る
5. コンポーネントは、css、html、tsの3つでワンセット。ng generateでひな形追加
    - todo-index.component.xxx
    - app-todo-index セレクタ
    - app/app.module.tsに追加
6. 標準コンポーネントについては以下
    - 標準コンポーネントも、app/app.module.tsのimports:に追加
7. MVCライク
8. DIコンテナあり
9. 個々のコンポーネントのcssについて
    - 子コンポーネント等に影響を与えない
    - 自分自身は:hostセレクタを使用する

標準クラス 抜粋

```text
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormBuilder } from '@angular/forms';

// app.module.ts に追加が必要
import { FormsModule, ReactiveFormsModule } from '@angular/forms';
```

コンポーネントの概要（@Component()）

```text
@Component({
  selector: 'app-todo-index',
  templateUrl: './todo-index.component.html',
  styleUrls: ['./todo-index.component.css'],
})

// providersを書いてサービスを指定もできる
```

コンポーネントやディレクティブと入れ子（@Input()、@Output()）

```text
@Input() 親→子の子側に設定
@Output() 子→親
・プロパティ変更傍受は、ngOnChanges()よりsetterを推奨
```

ライフサイクルInterface 抜粋

```text
OnInit.ngOnInit()
OnDestroy.ngOnDestroy()
```

リアクティブフォーム

```text
- formタグ/inputタグなどを対応付けて、サポートする機能
- 複雑なバリデーション機能を構築する場合、直接バインドするより使いやすい
- .html [formGroup]="xxx"
- .html formControlName="yyy"
- FormBuilderは、単に「new FormControl(), new FormControl()...」と同じ
- FormGroupインスタンス.get('yyy')
- FormGroupインスタンス.patchValue('yyy', ...)
```

ディレクティブ

```text
- html属性を自作するための機能
- @HostListener('event name')で、設定されたタグのイベントに対するハンドラを定義できる
```

ページ遷移前後のチェック

```text
- CanActivate<T>
- CanDeactivate<T>
- ページに来ていい？、ページから離れていい？と確認する仕組み
- app-routing.module.tsで有効にする
- typescriptはダックタイピングなので、Interfaceやクラスを継承しなくても動く
    - 継承を記載しないのは非推奨
```

DI可能化

```text
- @Injectable()
- これが付いたクラスは、constructor()の引数にすると、newされて渡される
```

NgModuleの詳細

```text
declarations : すべてのコンポーネント・ディレクティブ・パイプを書く
imports      : すべてのモジュールおよびモジュールが依存しているクラスを記述する
providers    : インジェクションするサービスを記述する
bootstrap    : AppComponent
```

JsRxヘルパー関数

```text
of()   : 同期データをObservableでラップする
pipe() : パイプする
tap()  : ログなどを実現するために使う（受け取った値そのものには何もしない）
```