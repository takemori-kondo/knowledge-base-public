# front-06. Angular
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

Angular 日本語ドキュメンテーション - ユーザー入力  
https://angular.jp/guide/user-input

ES2018: Rest/Spread Properties  
https://2ality.com/2016/10/rest-spread-properties.html#spread-defines-properties-objectassign-sets-them

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

* Node.jsのバージョンを複数インストールする場合は、nvmを活用する必要あり。
* Node.jsを入れる過程で「自動的に...」をチェックしておくと、ChocolateyやPython3が入る
```

________________________________________
### 1.2. Angular CLIのヘルパーコマンド

新規プロジェクト作成と実行

```powershell
git --version
ng new front06
cd front06
ng serve
# ** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **
# と出れば、成功。上記URLでブラウザからアクセスできる
# 以降、vscodeで作業する場合はfront06をルートフォルダとして開くとよい
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

jsの補足

カテゴリ    |演算や項目                  |説明
------------|----------------------------|-------------------------------------------------------------------------
型の種類    |単純型                      |boolean, number, string。厳密にはsymbol（ES2015）、bigint（策定中）もある
型の種類    |その他の型                  |object, undefined, null
boolean     |==, !=                      |TypeScriptでは異なる型の比較はできないため==, !=でも問題にならない
boolean     |===, !==                    |上述の通り
型検査      |typeof a                    |非推奨：object型は詳細を見分けない
型検査      |'name' in a                 |プロパティ存在確認（プロトタイプ辿るため鈍足）
型検査      |a.hasOwnProperty('name')    |プロパティ存在確認（プロトタイプ辿らないため高速）※1
型検査      |if(a.name)                  |プロパティ存在確認（undefinedが返り、falseになる）※2
型検査      |a.constructor               |オブジェクトに対して使うことで、C#のGetType()相当
spread      | = ...src                   |srcの各要素を展開する。foreachと同じ。「同キーは最後の値で上書き」を活用してマージも可能
rest        |...dest =                   |それ以外のパラメータをこれにまとめる。paramsと同じ考え方
浅いコピー  |copied = { ...src }         |-
マージ      |Object.assign(dest, src)    |js固有の標準機構。destにsrcの各メンバーを（存在しなければメンバ追加も行う形で）適用する ※3
マージ      |{ ...src1, ...src2 }        |TSまたはES2018(ES9)以降で利用可能。スプレッド演算子によるメンバーマージ ※3
分割代入    |const { a1, a2 } = obj      |objのメンバが展開されて各const変数に代入される。宣言済み変数群に対して分割代入する場合は全体を()で囲む
htmlイベント|onclick="foo(event)"        |引数名はeventで固定。イベントに対してhtml属性はonXXXという名前で定義されている
htmlイベント|click, dblclick             |-
htmlイベント|focus, blur                 |blurはフォーカスが離れた時のイベント。バブリングするバージョンはfocusin, focusout
htmlイベント|keydown, keyup              |-

※1 ES2015で追加されたclass構文での継承メンバはサブクラスで定義したことになるため、trueが返る  
※2 falseや0と見分けがつかないのが難点だが、配列やオブジェクト型なメンバに対するチェックコードではよく見かける  
※3 ほぼ同様だが以下が異なる  
    Object.assignはsetter/getterを考慮・呼ばれるが、スプレッド演算子は考慮しない  
    Object.assignはdestの内容を書き換えるが、スプレッド演算子を活用した方法は新たなオブジェクトリテラルの生成である

分割代入・rest構文・spread演算子

```text
const { p1, p2 } = obj;              // 分割代入。obj.p1, obj.p2がそれぞれp1, p2に代入される
const { p1, p2, ...others } = obj;   // rest構文。obj.p1, obj.p2は上記と同様、それ以外のプロパティはothersプロパティのメンバになる
const merged = { ...src1, ...src2 }; // spread演算子。典型的なオブジェクトマージ例
const { p1: renamed } = obj;         // これがすごくわかりづらい。obj.p1をrenamedに代入する
const { p1, p2 {pp1} } = obj;        // ネスト内の値を代入したい時。pp1にobj.p2.pp1が代入される

function foo({p1, p2}) {
    // ...
}
foo(obj);                            // 仮引数も左辺と同じ

// objectでしか例にしていないが、連想配列同士でもほぼ同様の事が可能
```

TypeScript は jsに以下を追加したもの

1. 本質的に型に関する情報の付加と対応する型検査をしているだけのもの
    - 型指定、型union指定、タプル(制限付き配列)
    - 動的プロパティ禁止
    - 暗黙キャスト禁止
    - 形状の違うオブジェクト禁止、オプションプロパティ
    - 互換性のないシグネチャ禁止、通常引数、オプション引数
    - 抽象クラス、interface、type、型パラメータ、アクセス修飾子、readonly、static、final
    - enum(非推奨)
2. 明らかにダメなプラクティスに対する制限
    - 初期化していない変数の利用の禁止
    - コンストラクタ終了までに初期化されないプロパティの禁止
3. 便利なため追加された文法
    - classのプロパティの書き方を緩和
    - 属性（tsではデコレータと呼ぶ）
    - 末尾カンマの許容

基本事項（C#との比較）

項目                    |C#名          |C#文法例                       |ts名、補足等                    |ts文法例
------------------------|--------------|-------------------------------|--------------------------------|------------------------------------------
モジュール参照          |アセンブリ参照|アセンブリ毎                   |import/export（ES2015）         |クラス毎
import                  |using         |using System;                  |import（ES2015）                |import { Component } from '@angular/core';
export                  |-             |-                              |export（ES2015）                |export class Foo {
class                   |-             |public class Foo               |（ES2015）                      |class Foo {
継承                    |-             |public class Bar : Foo         |（ES2015）                      |class Bar extends Foo {
実装                    |-             |public class Baz : IAbc        |-                               |class Baz implements IAbc {
アノテーション          |属性          |[HttpGet]                      |デコレータ                      |@Component(
コンストラクタ          |-             |public Foo()                   |（ES2015）                      |constructor(
プロパティ自動宣言代入  |-             |-                              |-                               |constructor(private fb: FormBuilder
メンバ変数              |フィールド    |public Form form;              |プロパティ                      |public form: FormGroup;
メンバ関数（メソッド）  |-             |private void ClearForm()       |-                               |private clearForm() : void {
async関数-戻り値無し    |-             |async Task DelayAsync()        |-                               |async delayAsync() : Promise<void> {
async関数-戻り値あり    |-             |async Task<int> GetCountAsync()|-                               |async getCountAsync() : Promise<number> {
await                   |-             |await foo.DelayAsync();        |（ES2015）                      |await foo.delayAsync();
ローカル変数            |-             |string name = "Alice";         |-                               |let name: string = "Alice";
ローカル変数-readonly   |-             |-                              |-                               |const name: string = "Alice";
単純型の種類            |-             |bool, int, floatなど           |-                               |boolean, number, string
静的配列                |-             |int[] values;                  |-                               |-
配列                    |-             |List<int> values;              |-                               |const values: number[] = this.getValues();
型推論による変数宣言    |var           |var a = "Bob";                 |unknown                         |let a: unknown = "Bob";
型推論の放棄（非推奨）  |dynamic       |dynamic a;                     |any                             |let a: any;
匿名型                  |-             |var an = { A = 1, B = 2 };     |オブジェクトリテラル            |let obj = { a: 1, b: 2, };
メンバ存在チェックhack  |-             |-                              |-                               |if (obj.c && otherExpression) { ※1
名前付き引数            |-             |Foo(width: 10, height: 20);    |仮引数をオブジェクトにする      |foo(state: { width: number, height: number }) {
可変長引数              |-             |Bar(params int args[])         |レストパラメータ                |bar(...args:number[]) {
匿名関数・ラムダ式      |-             |func1 = (x, y) => x * y;       |-                               |func1 = (x, y) => x * y;
型アサーション          |-             |-                              |-                               |let a: FooType = anyVar as FooType;
文字列補完              |-             |var text = $"...{x}...";       |テンプレートリテラル（ES2015）  |let text: string = `...${x}...`;
foreach                 |-             |foreach(item in list)          |（ES2015）                      |for(let item of list) {
オブジェクトマージhack  |-             |-                              |-                               |{ ...src1, ...src2 } ※1
末尾のカンマ            |-             |許容されない                   |（ES2015）                      |許容される
アクセス修飾子デフォルト|-             |状況によりさまざま             |（ES2015）                      |いずれもpublic
delegate定義            |-             |delegate void Foo(int a);      |呼び出しシグネチャ              |type Foo = (number a) => void;

※1 「jsの補足」で前述の通り

Typescript特有の考え方

1. 構造的な型チェック（継承や実装を宣言しなくてよい）
    - 宣言ではなく実際の型構造で静的検証する
    - is aではなくlooks like a
    - ダックタイピング
2. オプションプロパティ「public a?: int」
    - そのメンバがいなくてもよい
    - 他言語のnullableではない
3. オーバーロード・オーバーロード解決周りの考え方
    - C#/Javaのようなシグネチャ違いのオーバーロードは許容されない代わりにオプション引数と型unionで設定が可能

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

tsconfig.json の推奨（ver.4.3.5）

- strict
    - alwaysStrict（常にstrictモード）
    - noImplicitAny（暗黙any変数はNO）
    - noImplicitThis（OOP以外でthisはNO）
    - strictBindCallApply（call/bind/applyでシグネチャが正しいか検証する）
    - strictFunctionTypes（グローバル関数と関数オブジェクト間ではシグネチャをチェック）
    - strictNullChecks（既定の挙動をnull条件演算子相当にはしない）
    - strictPropertyInitialization（コンストラクタまでに全プロパティの初期化を必須にする）
    - useUnknownInCatchVariables（例外オブジェクトは型指定省略するとunknown）
- noFallthroughCasesInSwitch（switch文でフォールスルーはNO）
- noImplicitOverride（暗黙overrideはNO。Angularの初期設定はfalse）
- noImplicitReturns（return書き忘れはNO）

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

テンプレート構文抜粋

|構文                                                    |記法                                                             |サニタイズ
|--------------------------------------------------------|-----------------------------------------------------------------|-------------
|テンプレート自体                                        |htmlと同等                                                       |されない
|補間（Interpolation）                                   |{{ expression }}、属性="{{ expression }}"                        |される
|テンプレート文<br>（テンプレート上で認識されるtsの詳細）|テンプレートに制御を書くのはアンチパターンなので略               |-
|プロパティバインディング                                |[プロパティ]="expression"                                        |される
|属性バインディング                                      |[attr.属性]="expression"                                         |される
|Classバインディング-単一                                |[class.クラス名]="boolExpression"                                |される
|Classバインディング-マルチ(v9以降からサポート)          |[class]="json or stringList"                                     |される
|Styleバインディング-単一                                |[style.CSSプロパティ]="expression"                               |される
|Styleバインディング-マルチ(v9以降からサポート)          |[style]="json or stringList"                                     |される
|Eventバインディング                                     |(click)="foo($event)"                                            |-
|Built-in directives Htmlイベント                        |click, focus, blur, dblclickなど                                 |-
|Built-in directives ngClass(v8以前からあるやつ)         |マルチとほぼ同一                                                 |される
|Built-in directives ngStyle(v8以前からあるやつ)         |マルチとほぼ同一                                                 |される
|Built-in directives ngModel                             |双方向バインディング2を参照のこと                                |される
|Built-in directives ngIf                                |*ngIf="expression"                                               |-
|Built-in directives ngFor                               |*ngFor="let todo of todos"                                       |-
|Built-in directives ngFor                               |*ngFor="let todo of todos; let i = index; trackBy: hoge"         |-
|テンプレート参照変数（HtmlElement参照）                 |#bar                                                             |-
|Input & output @Input                                   |[@Inputなプロパティ]="expression"                                |される
|Input & output @Output                                  |(@OutputなEventEmitterプロパティ)="foo($event)"                  |-
|Input & output @Output                                  |(@OutputなEventEmitterプロパティ)="プロパティ=$event"            |-
|Input & output 双方向バインディング1                    |[(@Inputなxおよび@OutputなxChangeプロパティ)]="プロパティ"       |される
|Input & output 双方向バインディング2                    |[(ngModel)]="プロパティ"                                         |される

-

セキュリティ要約

```text
可能な限り、Angularの補間やバンディングに頼ってください。直接DOMとやり取りしないでください
補間ならば文字参照に置換、バインディングならscriptやonerrorの記載を取り除きます

1. 既定のトランスパイラを使用せずにテンプレート自体を動的に生成する、などのアプローチはサニタイズされないので止めてください  
2. 部分的なサーバサイドレンダリング等も同様の問題を抱えるため、可能なら避けてください
3. Angularのバインディングや補間に頼らず、ネイティブのDOM APIを操作すると保護されません
```

組み込み済みコンポーネント抜粋（=独自タグ）

|コンポーネント              |役割
|----------------------------|-----------------------------------------------
|router-outlet               |ルーティングされるページの置き換え位置
|ng-content                  |componentが子タグを受け入れれる際の置き換え位置
|ng-template と ng-container |条件付きで表示する場合に使用。かなり冗長

-

各種補足

1. コンポーネントやディレクティブを自作する際、selector名とプロパティ名を同名にすると、利用側はselector名を省略可能
2. Html自体の属性やプロパティは、適当なHtmlのヘルプを参照のこと
3. Html自体の属性やプロパティは直接記述せず、各種バインディングすること（Angularの機能を使うことでXSS対策になる）
4. bind-xxxは[xxx]の別の書き方。同じ意味
5. on-xxxは(xxx)の別の書き方。同じ意味
6. bindon-xxxは[(xxx)]の別の書き方。同じ意味
7. バインディング等の右辺の記述は、厳密には式のみ許容（式コンテキスト）、複数の文を許容するもの（文コンテキスト）がある

その他のテンプレート構文機能

- パイプ
    - {{ title | uppercase }}
- Null条件演算子（Safe navigation operator）
    - {{ foo?.name }}
    - テンプレート中ではTypescript3.6以前でも使用可能（特別な処理がなされている）
- anyにキャスト
    - {{ $any(foo).name }}
- Observable（Asyncパイプ）
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
@Input()  親→子（子コンポーネントが  通常の属性を持ち、親が値を渡せる。<child [foo]="parentMember">）
@Output() 子→親（子コンポーネントがイベント属性を持ち、親がハンドルできる。<child (bar)="onHandle($event)">）
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

ディレクティブ例

```text
@Directive({
  selector: 'button[appModal]'
})
export class ModalDirective implements AfterViewInit, OnDestroy {
  @Input() config: AppModalConfig;
  @Input() data: (event: AppModalEvent) => Promise<any>;
  @Output() done: EventEmitter<AppModalResult> = new EventEmitter<AppModalResult>();
  @Input() input: Element;
  // ...
  @HostListener('click', ['$event'])
  async onClick(e?: MouseEvent) {
    // ...
  }
}
```

ディレクティブ

```text
- html属性を自作するための機能
- ディレクティブに定義された@Inputや@Outputは、この属性を付与したタグにさらに属性を追加する
- @HostListener('eventName', ['$event'])で、設定されたタグのイベントに対するハンドラをフック出来る
    - イベント名にはonはつけない
    - clickに対する引数で受け取るのをtargetにしたければ、@HostListener('click', ['$event'])
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

________________________________________
## 4. AG-Grid with Angular
________________________________________
概要

```ts
// import { ActivatedRoute } from '@angular/router';
// import { FormGroup, FormBuilder } from '@angular/forms';
import { GridOptions, GridApi, ColGroupDef, ColDef, ColumnApi } from 'ag-grid-community';

  // public form: FormGroup;

  public gridOptions: GridOptions;
  private gridApi: GridApi;
  private gridColumnApi: ColumnApi;
  
  private dataSource: FooEntity[];
  
  constructor(
    // private route: ActivatedRoute,
    // private fb: FormBuilder,
  ) {
    // this.setupForm();
    this.setupGrid();
  }
  
  public onGridReady(params) {
    this.gridApi = params.api;
    this.gridColumnApi = params.columnApi;
    this.gridApi.setRowData([]);
  }
  
  private setupGridOptions() {
    this.gridOptions = {} as GridOptions;
    this.gridOptions.rowHeight = 24;
    this.gridOptions.headerHeight = 24;
    this.gridOptions.animateRows = true;
    this.gridOptions.domLayout = 'autoHeight';
    this.gridOptions.rowSelection = 'multiple';
    this.gridOptions.suppressContextMenu = true;
    this.gridOptions.suppressDragLeaveHidesColumns = true;
    
    this.gridOptions.defaultColDef = {
      editable: false,
      filter: true,
      filterParams: { selectAllOnMiniFilter: true },
      menuTabs: ['filterMenuTab'],
      resizable: true,
      sortable: true,
    };
    this.gridOptions.columnDefs = getGridColumnDefs();
    // this.gridOptions.localeText = ...
    // this.gridOptions.sideBar = ...
  }
  
  private getGridColumnDefs(): (ColDef | ColGroupDef)[] {
    return [
      {
        headerName: '画像名',
        field: 'ImageName',
        width: 80,
        cellClass: () => ['required-cell', 'editable-cell'],
        editable: true,
        filter: 'agTextColumnFilter',
        pinned: 'left',
      },
      {
        headerName: '対象',
        field: 'Target',
        width: 80,
        cellRenderer: params => {
          var input = document.createElement('input');
          input.type = "checkbox";
          input.checked = params.value;
          input.addEventListener('change', function(event) {
            params.data.Target = input.checked;
          });
          return input;
        },
        editable: true,
        filter: false,
        pinned: 'right',
        resizable: false,
      }
      // ...
    ];
  }
    
  private getData() {
    // ...
    this.gridApi.setRowData(this.dataSource);
  }
  
  private updateDataSource() {
    this.gridApi.stopEditing();
    // ...
    for(let item of this.dataSource) {
      item.Property1 = "...";
      item.Property2 = "...";
    }
    // ...
    this.gridApi.refreshCells();
  }
  
  private removeGridRows() {
    const selecteds = this.gridApi.getSelectedRows();
    for (let item of selecteds) {
      item.RemovedByUI = true;
    };
    this.gridApi.updateRowData({ remove: selecteds });
  }
  
  private addGridRow() {
     let a = new FooEntity();
     this.dataSource.push(a);
     this.gridApi.updateRowData({ add: [a] });
  }
```

要点

```text
1. GridOptions、onGridReady(params) を用意してテンプレートのag-grid-angularとバインド
2. onGridReadyの引数から、gridApi、gridColumnApiを取得
3. setRowData(any[])でバインド
4. ColDefのfieldにdataSourceと対応付けて、バインディングする
5. UIから値を変更した場合にdatasourceも変わる（双方向バインディング）
    https://www.ag-grid.com/javascript-data-grid/data-update/
    https://www.ag-grid.com/javascript-data-grid/cell-editing/
    ※ ver23からはupdateRowData()は非推奨、代わりにapplyTransaction()になった
    旧バージョンのリファレンスはこちら
    http://54.222.217.254/javascript-grid-data-update/
6. dataSourceで値を変えた後は、refreshCells()を呼び出してUIに反映する必要がある
    https://www.ag-grid.com/javascript-data-grid/data-update/
    https://www.ag-grid.com/javascript-data-grid/view-refresh/
7. 後からdataSourceに要素を追加や削除しても、AG-Grid側には反映されない
    - updateRowData({add: [item]}), dataSource.push(item)の両方をする必要がある
    - updateRowData({remove: [item]}), item.RemovedByUI = trueの両方をする必要がある
8. セルのbooleanとcheckboxのバインドは直接サポートされない
    - cellRendererに自前でaddEventListenerされたInputElementを用意して返すことで実現する
```