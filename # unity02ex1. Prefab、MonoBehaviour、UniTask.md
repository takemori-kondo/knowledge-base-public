# unity02ex1. Prefab、MonoBehaviour、UniTask
________________________________________
## 1. Prefabのプラクティス
________________________________________
ネスト状のプレハブ  
https://docs.unity3d.com/ja/2022.3/Manual/NestedPrefabs.html
________________________________________
### 1.1. Prefab編成とヒエラルキー


Prefabの編成

1. Prefabは、WinFormのコントロール、あるいはReactやAngularのコンポーネントのように作る
    - PrefabルートにPrefabと同名のMonoBehaviour継承クラスをアタッチする
    - Prefabは細かく分け、nestされたPrefabで編成する
    - 例：WeaponsUI、WeaponItemUI、WeaponDetailUIに分けて作り、nestする
    - 50のTipsのactorパターンは、これを更に一般化したものと言える
2. Prefab配下のコンポーネントへの参照は、[SerializeField]で一元管理する
    - ただし、デザイナーやプランナーにまでこれは求めない。後でプログラマがリファクタする
    - 例：プログラマはButtonコンポーネント側でイベントハンドラを紐づけない。SerializeFieldで参照し、Awakeなどでハンドラ登録する
        - 特にPrefab Variantでスクリプト差し替えを視野に入れると、この作法は重要
3. 子Prefabは親Prefabを知らない。親Prefabに依存しない（親が子を知り、子に必要な情報だけを渡す）
    - LoadData()など用意し、引数で受け取る
    - 親が子のLoadData()を呼び出す
4. 子Prefab内への参照は基本的に越権行為なので避ける

ヒエラルキー構成の詳細

1. 1つのGameObject・ノードに何でもかんでもアタッチしない
    - 適切にネスト・木構造する
    - 制御MonoBehaviourは、Rootノードにアタッチする(前述の通り)
    - 必要に応じて、AnimatorやDOTweenが制御するTransformを別にするためにネストする
    - 更にその下に、MeshやSpriteはなどをアタッチするノードを用意する
        - ただし、不可分かつ差し替えの想定もないなら、過剰に木構造に分割しなくてもよい
2. SerializeFieldは、publicフィールドより、SerializeFieldなprivateフィールドを好め
3. SerializeFieldは、GameObject型より、具象型を好め

PrefabのTips

- ヒエラルキーに配置されたPrefabインスタンスは、青筋で変更の有無が分かる
- ネストされたCanvasにおけるScale0問題などは、別記事の「Unity UI」を参照の事

________________________________________
### 1.2. 外部ライブラリとPrefab Variant

- 「Sceneへ配置 > それをPrefab化」 で、Variantを作成可能

________________________________________
## 2. MonoBehaviourのプラクティス
________________________________________
UnityのMonoBehaviourとのお付き合いの仕方  
https://zenn.dev/allways/articles/97f5fd71537a23

1. namespace に必ず入れる
2. sealed 句はとりあえずつけてよい
3. SerializeFieldで依存性の解決を行う（GetComponentなどの利用は基本的に避ける）
4. MonoBehaviour はロジックへの参照を持つ（ゲームロジック＝ドメインコードを直接記述しない）
    - MonoBehaviourからのdelegateパターン
    - MonoBehaviourの各Messageを移譲せよ
        - 各種Unityイベントやイベントハンドラに対するゲームロジックも移譲先ロジックで処理せよ
    - pure C#で実装する（ロジッククラスはUnityへの依存を避ける。UIロジックは除く）
        - new で依存物を渡す（ドメインコードはコンストラクタインジェクションに従え）
            - UnityEngine.Objectへの参照を持つ（ロジッククラスはコンストラクタインジェクションでUnity側のオブジェクトを受け取っても良い）
            - UnityEngine.Objectへの参照を安全に使う（fake nullがいつでも発生しうるので、nullチェックを怠らない）
        - Disposeパターン（ドメインコードにIDisposableを実装しておくとusing出来てよい）
5. OnEnableで初期化しOnDisableで破棄する、またはAwakeで初期化しOnDestroyで破棄する
6. 明示的 null代入
7. 基本的にinternal にしとけ
8. 処理タイミングを厳密にコードで定義する（UpdateやLateUpdateを使うより、ゲームループは自前で作ったほうが処理が明確）

________________________________________
## 3. UniTaskのプラクティス
________________________________________
UnityとUniTaskを使いこなしてちゃんと中断できるようにする  
https://zenn.dev/allways/articles/cf2f87244128fe

メリット

1. コルーチンと同じく非スレッド
2. コルーチンと異なり、async/awaitの文法に完全に対応し、ネストが容易
3. コルーチンと異なり、PlayerLoopTimingで実行タイミングを柔軟に指定可能

要約

通常                 |UniTask
---------------------|-----------------
async Task foo()     |async UniTask foo()
async Task\<T> foo() |async UniTask\<T> foo()
async void foo()     |async UniTaskVoid foo()
Task.Delay()         |UniTask.Delay()
Task.Run()           |UniTask.Create()
Task.WhenAll()       |UniTask.WhenAll()
Action               |UniTask.Action(async () => {})
UnityAction          |UniTask.UnityAction(async () => {})
action();            |UniTask.Void(async () => {});
yield return null    |await UniTask.NextFrame()

.


基本的な中断要求に対する考え方

1. 開始時に中断要求されていたら何もしない
2. 実行中に中断要求があればなるはやで受け入れる
3. 完了後に中断要求があれば完了してよい(完了したので中断できない)

CancellationTokenについて

1. 中断可能なメソッドは常に必須パラメータにしろ
2. 中断不可能なメソッドについては渡せないようにしろ

PlayerLoopTiming抜粋

値               |意味
-----------------|--------------
FixedUpdate      |全てのFixedUpdateの前
LastFixedUpdate  |全てのFixedUpdateの後
Update           |全てのUpdateの前
LastUpdate       |全てのUpdateの後
PreLateUpdate    |全てのLateUpdateの前
LastPreLateUpdate|全てのLateUpdateの後

.