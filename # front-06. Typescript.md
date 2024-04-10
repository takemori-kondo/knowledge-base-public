# front-06. Typescript
________________________________________
プログラミングTypeScript  
https://www.oreilly.co.jp/books/9784873119045/

TypeScript 日本語ハンドブック | js STUDIO  
https://js.studio-kingdom.com/typescript/

ES2018: Rest/Spread Properties  
https://2ality.com/2016/10/rest-spread-properties.html#spread-defines-properties-objectassign-sets-them

________________________________________
## 1. Prepare
________________________________________
### 1.1. 用意

※ 注意

```text
この方法はあくまでTypescriptの学習を目的としています。
三大フレームワーク環境下では、より最適な手順があるためそれに従ってください。
なお、今回のサンプルのtsconfig.jsonはAngular13に近い記載になっています。
```

Download by npm

```powershell
cd front06
npm init -y
npm i -D typescript
npm i -D eslint @typescript-eslint/eslint-plugin @typescript-eslint/parser
npx tsc --init
# tsconfig.jsonは以下のように書き換え
```

tsconfig.json

```json
{
  /* Visit https://aka.ms/tsconfig to read more about this file */
  "compilerOptions": {
    "baseUrl": "./",
    "outDir": "./dist/out-tsc",
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "sourceMap": true,
    "declaration": false,
    "downlevelIteration": true,
    "experimentalDecorators": true,
    "moduleResolution": "node",
    // "importHelpers": true,
    "target": "es2015",
    "module": "es2015",
    "useDefineForClassFields": false,
    "lib": [
      "es2015",
      "dom"
    ],
    "esModuleInterop": true,
    "skipLibCheck": true
  },
  "include": [
    "src"
  ],
}
```

eslintの初期設定

```powershell
npx eslint --init
    ? How would you like to use ESLint? ...
        > To check syntax and find problems
    ? What type of modules does your project use? ...
        > JavaScript modules (import/export)
    ? Which framework does your project use? ...
        > None of these
    ? Does your project use TypeScript?
        » Yes
    ? Where does your code run? ...  (Press <space> to select, <a> to toggle all, <i> to invert selection)
        > Browser
    ? What format do you want your config file to be in? ...
        > JSON
    @typescript-eslint/eslint-plugin@latest @typescript-eslint/parser@latest
    ? Would you like to install them now?
        » Yes
    ? Which package manager do you want to use? ...
        > npm

# 上記で初期化が終わった後、.eslintrc.json の extendsに以下を追加
## "root": true,
## ...
## "extends": [
##     ...
##     "plugin:@typescript-eslint/recommended-requiring-type-checking"
## "parserOptions": {
##     "project": "./tsconfig.json",
# 最終的には次の.eslintrc.jsonの通り
```

.eslintrc.json

```json
{
    "root": true,
    "env": {
        "browser": true,
        "es2021": true
    },
    "extends": [
        "eslint:recommended",
        "plugin:@typescript-eslint/recommended",
        "plugin:@typescript-eslint/recommended-requiring-type-checking"
    ],
    "overrides": [
    ],
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
        "project": "./tsconfig.json",
        "ecmaVersion": "latest",
        "sourceType": "module"
    },
    "plugins": [
        "@typescript-eslint"
    ],
    "rules": {
    }
}
```

Es-lint check & build

```powershell
# eslintの実施(全てのソースはsrcサブフォルダ下にあるとした場合)
# 別の方法では、予め.eslintrc.json に"ignorePatterns"で除外するフォルダを選んから、「.」で実施してもいい
npx eslint src/**

# ビルド
npx tsc --build
```

________________________________________
### 1.2. Hello world

FrontSamples\front06\src\sample01-hello-world.ts

```ts
console.log('Hello TypeScript!')
```

________________________________________
## 2. Samples
________________________________________
TypeScript samples

- FrontSamples/front06/typescript-samples.html
- FrontSamples/front06/tsconfig.json
- FrontSamples/front06/.eslintrc.json
- FrontSamples/front06/src/index.ts
- FrontSamples/front06/src/sample01-hello-world/index.ts
- FrontSamples/front06/src/sample02-syntax/sample1-type1.ts
- FrontSamples/front06/src/sample02-syntax/sample2-type2.ts
- FrontSamples/front06/src/sample02-syntax/sample3-type3.ts
- FrontSamples/front06/src/sample02-syntax/sample4-etc.ts
- FrontSamples/front06/src/sample02-syntax/sample5-class.ts
- FrontSamples/front06/src/sample03-practices/practice1-property-check.ts
- FrontSamples/front06/src/sample03-practices/practice2-object-merge.ts
- FrontSamples/front06/src/sample03-practices/practice3-promise.ts
- FrontSamples/front06/src/sample03-practices/practice4-promise2.ts
- FrontSamples/front06/src/sample03-practices/practice5-cast-to-number.ts
- FrontSamples/front06/src/sample03-practices/practice6-cast-to-string.ts

________________________________________
## 3. Syntax
________________________________________
### 3.1. Typescript要約

typescriptの前に、JSの文法の復習

カテゴリ    |演算や項目                   |説明
------------|-----------------------------|-------------------------------------------------------------------------
型の種類    |9種類                        |boolean, number, string, symbol, bigint, object/null, undefined, function
boolean     |==, !=                       |曖昧な比較。使用禁止
boolean     |===, !==                     |厳密な比較
型検査      |typeof a                     |object型およびnullの場合、詳細はわからない
型検査      |'name' in a                  |プロパティ存在確認（プロトタイプ辿るため鈍足）
型検査      |a.hasOwnProperty('name')     |プロパティ存在確認（プロトタイプ辿らないため高速）※1
型検査      |if(a.name)                   |プロパティ存在確認（undefinedが返り、falseになる）※2
型検査      |a instanceof                 |オブジェクトに対して使うことで、C#のis演算子相当…なのだが、interfaceに対して使えない
型検査      |a.constructor                |オブジェクトに対して使うことで、C#のGetType()相当
spread      | = ...src                    |ES2018から利用可能。srcの各要素を展開する
rest        |function foo(...dest) { }    |ES2018から利用可能。可変引数のこと
浅いコピー  |copied = { ...src }          |spread演算子による浅いコピーの例
マージ      |Object.assign(dest, src)     |destにsrcの各メンバーを（存在しなければメンバ追加も行う形で）適用する ※3
マージ      |merged = { ...src1, ...src2 }|spread演算子によるメンバーマージ ※3
分割代入1   |const [ a1, a2 ] = ary       |aryの各要素の値が順に代入される。この例ではconst変数宣言と同時に行っている
分割代入2   |const { a1, a2 } = obj       |objのメンバの値が順に代入される。この例ではconst変数宣言と同時に行っている
htmlイベント|onclick="foo(event)"         |引数名は必ずevent。イベントに対してhtml属性はonXXXという名前で定義されている
htmlイベント|click, dblclick              |-
htmlイベント|focus, blur                  |blurはフォーカスが離れた時のイベント。バブリングするバージョンはfocusin, focusout
htmlイベント|keydown, keyup               |-

※1 ES2015で追加されたclass構文での継承メンバはサブクラスで定義したことになるため、trueが返る  
※2 falseや0と見分けがつかないのが難点だが、配列やオブジェクト型なメンバに対するチェックコードではよく見かける  
※3 この2つはほぼ同様だが以下が異なる  
    Object.assignはsetter/getterを考慮・呼ばれるが、スプレッド演算子は考慮しない  
    Object.assignはdestの内容を書き換えるが、スプレッド演算子を活用した方法は新たなオブジェクトリテラルの生成である

分割代入・rest構文・spread演算子

```typescript
const { p1, p2 } = obj;              // 分割代入。obj.p1, obj.p2がそれぞれp1, p2に代入される
const { p1, p2, ...others } = obj;   // rest構文。obj.p1, obj.p2は上記と同様、それ以外のプロパティはothersプロパティのメンバになる
const merged = { ...src1, ...src2 }; // spread演算子。典型的なオブジェクトマージ例
const { p1: renamed } = obj;         // これがすごくわかりづらい。obj.p1をrenamedに代入する
const { p1, p2: {pp1} } = obj;       // ネスト内の値を代入したい時。pp1にobj.p2.pp1が代入される

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
    - 抽象クラス、interface、type、型パラメータ、アクセス修飾子、readonly、static
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
モジュール参照          |アセンブリ参照|アセンブリ毎                   |import/export（ES2015）         |クラスまたは関数毎
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
async関数-戻り値あり    |※1           |async Task<int> GetCountAsync()|-                               |async getCountAsync() : Promise<number> {
await                   |-             |await foo.DelayAsync();        |（ES2015）                      |await foo.delayAsync();
ローカル変数            |-             |string name = "Alice";         |-                               |let name: string = "Alice";
ローカル変数-readonly   |-             |-                              |-                               |const name: string = "Alice";
単純型の種類            |-             |bool, int, floatなど           |-                               |boolean, number, string, symbol, bigint
静的配列                |-             |int[] values;                  |-                               |-
配列                    |※1           |List<int> values;              |-                               |const values: number[] = this.getValues();
型推論による変数宣言    |var           |var a = "Bob";                 |型を省略すれば良い              |let a = "Bob";
ダウンキャスト必須型    |-             |-                              |unknown                         |let a: unknown = "Bob";
型推論の放棄（非推奨）  |dynamic       |dynamic a;                     |any                             |let a: any;
匿名型                  |-             |var an = { A = 1, B = 2 };     |オブジェクトリテラル            |let obj = { a: 1, b: 2, };
メンバ存在チェックhack  |-             |-                              |-                               |if (obj.c && otherExpression) { ※1
名前付き引数の定義      |-             |通常通り                       |分割代入を利用                  |foo({ width: number, height: number }) {
名前付き引数の利用      |-             |Foo(width: 10, height: 20);    |分割代入                        |foo(obj);
可変長引数              |-             |Bar(params int args[])         |レストパラメータ                |bar(...args: number[]) {
匿名関数・ラムダ式      |-             |func1 = (x, y) => x * y;       |-                               |func1 = (x, y) => x * y;
型の明示                |ダウンキャスト|FooType a = (FooType)anyVar;   |型アサーション                  |let a: FooType = anyVar as FooType;
文字列補完              |-             |var text = $"...{x}...";     |※1 テンプレートリテラル（ES2015）|let text: string = `...${x}...`;
foreach                 |-             |foreach(item in list)          |（ES2015）                      |for(let item of list) {
オブジェクトマージhack  |-             |-                              |-                               |{ ...src1, ...src2 } ※2
末尾のカンマ            |-             |許容されない                   |（ES2015）                      |許容される
アクセス修飾子デフォルト|-             |状況によりさまざま             |（ES2015）                      |いずれもpublic
delegate定義            |-             |delegate void Foo(int a);      |呼び出しシグネチャ              |type Foo = (number a) => void;

※1 マークダウンビューアで見ると上手く表示されないのでソースを見てください

※2 「typescriptの前に、JSの文法の復習」で前述の通り

Typescript特有の考え方

1. 構造的な型チェック（継承や実装を宣言しなくてよい）
    - 宣言ではなく実際の型構造で静的検証する
    - is aではなくlooks like a
    - ダックタイピング
2. オプションプロパティ「public a?: int」
    - そのメンバがいなくてもよい
    - 他言語のnullableではない
3. オーバーロード解決周りの考え方
    - C#/Javaと異なり、各オーバーロードに実装を持つことが出来ない（宣言だけ記述し、定義は最後の宣言1つのみで記述する必要がある）
        - javascriptにオーバーロードの仕組みがないため、javascriptへコンパイルを簡略化するための措置

$の付く識別子

- 言語仕様としては特別な意味はない
- Chromeのデバッガは、Elementsタブで選択したElementは$0に入っている
- jsフレームワークは、たいていの場合特別な意味を持たせる慣習がある
    - jQueryの場合、$(str)関数があり、これが根源である
    - Angularの場合
        - Angularイベントの引数名は$eventで固定
- Observableに$接尾辞をつける慣習

________________________________________
### 3.2. その他

tsconfig.json に設定するべき推奨（ver.4.3.5）

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