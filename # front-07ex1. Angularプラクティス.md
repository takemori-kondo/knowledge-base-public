# front-07ex1. Angularプラクティス
________________________________________
## 1. 無理しない60点のAngular
________________________________________
### 1.1. コンポーネントとページ

1. WinForm感覚で1ページ1component
    - サブコンポーネントを作るか？＝WinFormで自作コントロールを作るか？
2. WinForm感覚でビハインドコードなts
    - ViewModel的なprivateメンバやプロパティ群
    - コンストラクタ/ハンドラ/ハンドラの専属従属ルーチン
    - 共有privateルーチン
3. godServiceを使用 (godService.postAsyncでビハインドコードからpost、など)
    - godServiceが嫌なら、実装の詳細はいくつかの別クラスで分割してService Facadeにする
    - 詳細後述

________________________________________
### 1.2. オレオレフレームワークのための共通パーツ

非ドメインUtil

1. DebounceなMethodDecorator
2. 日付ピッカー
3. date系Util (toYYYYMMDD, toYYYYMM, toYYYY, clamp, toDate, toClampedDate)
4. object系Util (isNullish, isObject, removeNullish, stringifyEquals, clone, toBase64, fromBase64)
5. promise系Util (delay)
6. 正規表現定数 (INTEGER, WORD, KATA, HIRA, WORD_SPACE, KATA_SPACE, HIRA_SPACE)
7. string系Util (isNullishOrWhitespace)

非ドメイン系Util (Angular機能)

- animationsに指定する共通パーツ

オレオレフレームワークのカスタマイズポイントに対する規定実装

1. ErrorHandler (既定実装：Promise対応)
2. appIsAuthorizedGuard (CanActivateFn)
3. appIsSavedGuard (CanDeactivateFn)
4. Ajaxスタブ
5. godService (後述)

________________________________________
### 1.3. godService

要点

- 概ねすべてのページで利用される操作ヘルパーの集合体
- 注入するのが分かりきっているため、1つのgodService (ServiceFacade) にまとめる
- ただし本当に1つのクラスにべた書きすると1000行を平気で超えるため、いくつかのサブServiceに分けてサブServiceを束ねるのが良い

典型的な操作や保持データ

- AppAjaxService (HttpClientにデフォルトAPIエンドポイント、429or503リトライを追加したラッパー)
- AppAuthService (認証やページ認可の検証とログインダイアログの呼び出しなどの制御)
- AppMessageBoxService (確認ダイアログ)
- AppModalService (任意のcomponentを使用するダイアログ)
- AppLocaleService (多言語リソースからテキストを取得)
- AppValidators (Validatorsに機能を加えたstaticラッパ関数群と、formGroupバリデート実施関数)

その他の汎用制御

1. クッキー(getCookieValue)
2. ダイアログ(showConfirm)
3. location制御(back, replaceState)
4. Router制御(navigate, navigateWithObject, getNavigateObject)
5. PageData制御1(setPageDataToSession, getPageDataFromSession, setPageDataToLocal, getPageDataFromLocal)
6. PageData制御2(keepGrid, restoreGrid, keepPagination, restorePagination, keepForm, restoreForm)

________________________________________
### 1.4. AG-Grid関連

1. AG-Grid用の自作Vaidator (だいぶ作りこむ前提)