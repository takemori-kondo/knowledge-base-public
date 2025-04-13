# unity03ex2. Practice2 Prefab、MonoBehaviour、Object、UniTask
________________________________________
ネスト状のプレハブ  
https://docs.unity3d.com/ja/2022.3/Manual/NestedPrefabs.html

UnityのMonoBehaviourとのお付き合いの仕方  
https://zenn.dev/allways/articles/97f5fd71537a23

UnityとUniTaskを使いこなしてちゃんと中断できるようにする  
https://zenn.dev/allways/articles/cf2f87244128fe
________________________________________
## 1. Prefabのプラクティス
________________________________________
Prefabの編成の仕方

1. Prefabは、WinFormのコントロール、あるいはReactやAngularのコンポーネントのように作る
    - PrefabルートにPrefabと同名のMonoBehaviour継承クラスをアタッチする
    - Prefabは細かく分け、nestされたPrefabで編成する
    - 例：WeaponsUI、WeaponItemUI、WeaponDetailUIに分けて作り、nestする
2. Prefab配下のコンポーネントへの参照は、[SerializeField]で一元管理する
    - ただし、デザイナーやプランナーにまでこれは求めない。後でプログラマがリファクタする
    - 例：プログラマはButtonコンポーネント側でイベントハンドラを紐づけない。SerializeFieldで参照し、Awakeなどでハンドラ登録する
        - 特にPrefab Variantでスクリプト差し替えを視野に入れると、この作法は重要
3. 子Prefabは親Prefabを知らない。親Prefabに依存しない（親が子を知り、子に必要な情報だけを渡す）
    - LoadData()など用意し、引数で受け取る
    - 親が子のLoadData()を呼び出す
4. 子Prefab内への参照は基本的に越権行為なので避ける

PrefabのTips

- ヒエラルキーに配置されたPrefabインスタンスは、元のPrefabから変更があるかは青筋で有無が分かる
- ネストされたCanvasにおけるScale0問題などは、別記事の「Unity UI」を参照の事

________________________________________
## 2. MonoBehaviourのプラクティス
________________________________________

1. namespace に必ず入れる
2. sealed 句はとりあえずつけてよい
3. SerializeFieldで依存性の解決を行う（GetComponentなどの利用は基本的に避ける）
4. MonoBehaviour はロジックへの参照を持つ（ゲームロジック＝ドメインコードを直接記述しない）
    - MonoBehaviourからのdelegateパターン
    - MonoBehaviour.Messageを移譲せよ（各種Unityイベントやイベントハンドラに対するゲームロジックもロジックで処理せよ）
5. OnEnableで初期化しOnDisableで破棄する、またはAwakeで初期化しOnDestroyで破棄する
6. new で依存物を渡す（ドメインコードはコンストラクタインジェクションに従え）
7. Disposeパターン（ドメインコードにIDisposableを実装しておくとusing出来てよい）
8. 明示的 null代入
9. pure C#で実装する（ロジッククラスはUnityへの依存を避ける。UIロジックは除く）
10. 基本的にinternal にしとけ
11. UnityEngine.Objectへの参照を持つ（ロジッククラスはコンストラクタインジェクションでUnity側のオブジェクトを受け取っても良い）
12. 処理タイミングを厳密にコードで定義する（UpdateやLateUpdateを使うより、ゲームループは自前で作ったほうが処理が明確）
13. UnityEngine.Objectへの参照を安全に使う（fake nullがいつでも発生しうるので、nullチェックを怠らない）

________________________________________
## 3. UnityEngine.Objectのプラクティス
________________________________________
UnityEngine.Object継承クラスとDestroyの注意点（fake null問題）

- !=、==は、null扱い
- null合体演算子は、null扱いにならない
- null条件演算子は、null扱いにならない

________________________________________
## 4. UniTaskのプラクティス
________________________________________
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