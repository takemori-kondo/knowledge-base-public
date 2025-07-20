# unityPractices02. Other Practices
________________________________________
初心者が気をつけるべきUnity開発11のポイント  
https://shibuya24.info/entry/unity-be-careful

Unity 開発に関する 50 の Tips 〜ベストプラクティス〜（2016 Edition）  
https://shikaku-sh.hatenablog.com/entry/unity-tips-and-best-practices-2016

Unity Technologies Japanさんのプロフィール | ドクセル  
https://docswell.com/user/UnityJapan
________________________________________
## 1. プラクティス要約
________________________________________
Unityの基本的なこと

1. Gitで管理する
2. ツールの更新はチーム全員同時に行う
3. プロジェクトの方針や規約はドキュメント化しておく
    - フォルダ構造
    - シーン構造
    - Layer、Sorting Layer
    - ビルド方法
    - 定型コードの規約
4. 最初にスケール等を決定。例えば
    - 1unity unit = 1m
    - Canvas Scalerは720p
    - 3Dモデルの原点は足元
5. 全てのシーンを簡単に実行できるようにしておく
    - 全てのシーンは可能な限り独立させる
    - 検証用シーンやプロトタイプシーンは分離可能にする
6. シーンはノードをフォルダのように使い整理
    - 実行中にInstantiateされたオブジェクトも散らかさない
7. Prefabを好め
    - 全てPrefabにする
    - 細かくPrefab化する
    - Prefabをネストする
8. デバッグ機能（特定の状態のアンロックなど）を用意する

典型的なプログラミングスタイルの推奨

1. 文字列リテラルによる検索・ロード・呼び出し・リフレクションを避ける
    - GameObject.FindWithTag
    - Resources.LoadAsync
    - SendMessage
2. nullをぼんやり使うことを避ける （明確な意図なくnullを許容することを避ける）
3. コルーチンよりUniTask
4. 定型処理は拡張メソッドを積極的に用意する
5. 同じことは、同じようにやる
    - イベント登録
    - Load
    - Instantiate
    - Addressable Assetの操作
    - 非同期の書き方
    - Web APIへのアクセス
    - UIが表示するデータの受け渡し方法
    - 一覧系UIのUI制御
    - 実行順の制御
    - 描画順の制御
    - Inアニメーション、Outアニメーションの制御
    - 入力の制御
6. 契約による設計 （許容しない引数や状態かどうか調べ、直ちに例外を返す）
7. publicフィールドを避ける
8. 表示とロジックを分離 （DDD）
9. 入力とロジックの分離 （DDD）
10. config、currentState、prevStateどれであるか明確にすること
11. 変な配列の使い方を避ける

________________________________________
## 2. 項目別のプラクティスの詳細
________________________________________
### 2.1. GameObjectとシリアライズのプラクティス

Prefabに関して

1. 1つのGameObjectやPrefabに何でもかんでもアタッチしない
    - ゲームオブジェクトのスクリプトは、Rootオブジェクトにアタッチする
    - ゲームオブジェクトのMeshやSpriteは、Childオブジェクトにアタッチする
    - ただし、不可分かつ差し替えの想定もないなら、無理に木構造に分割しなくてもよい
2. SerializeFieldは、publicフィールドより、SerializeFieldなprivateフィールドを好め
3. SerializeFieldは、GameObject型より、具象のMonoBehaviour型を好め
    - アサイン可能なPrefabを限定できるし、GameObjectからMonoBehaviourを取得するのも省略できる
4. 親Prefabは、ネストされたPrefab内の要素を直接参照するな
5. Prefabと対応するスクリプトは同名にする

ScriptableObjectに関して

1. ScriptableObjectは変則的な挙動を示すため、直接利用せずPrototypeとして使用し、Instantiateするとよい

________________________________________
### 2.2. 検索のプラクティス

1. GameObjectやComponentの探索は便利な反面、探索コストが重い。キャッシュすること（Find系は可能ならば避ける）
    - Findは、現在アクティブなシーン全体からGameObjectを探索する。重い操作になりうる
    - FindWithTag、FindObjectOfType、もFindとほぼ同様
    - FindGameObjectsWithTag、FindObjectsByTypeもFindとほぼ同様
    - GetComponent(s)は、そのGameObjectにアタッチされているComponentを探索する
    - GetComponent(s)InChildrenは、そのGameObjectと子孫全体からComponentを探索する。重い操作になりうる
    - GetComponent(s)InParentは、そのGameObjectと祖先全体からComponentを探索する。重い操作になりうる

________________________________________
### 2.3. デバッグ、負荷、ファイルサイズのプラクティス

1. Consoleはcolorタグを認識して色分け可能。検索も可能なので検索しやすくする
2. Linqは負荷が重いので避ける
3. Resourceフォルダ内のファイルを多くしすぎない

________________________________________
### 2.4. 設計のプラクティス

基本的なプラクティス

1. 初期データとステータスを分離する
2. GUI、コアロジック、キャラクターは疎結合にする

典型的なアクションゲームの場合

1. Controller。実際のオブジェクトやカメラにアタッチして動作させるScript
2. ModelあるいはGame Logic。Controllerからの要求を受け付け、自走する存在を表すScript
2. GeneratorあるいはSpawner。オブジェクトの生成管理するScript
3. ManagerあるいはDirector。空のオブジェクトにアタッチして全体管理する純粋なScript