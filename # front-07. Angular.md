# front-07. Angular
________________________________________
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