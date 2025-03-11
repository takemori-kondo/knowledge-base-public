# unity02. スクリプトとライフサイクル
________________________________________
スクリプティングの概念 - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/ScriptingConcepts.html

イベント関数の実行順序 - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/ExecutionOrder.html

UniTask(UniRx.Async)から見るasync/awaitの未来  
https://neue.cc/2018/12/25_571.html

Await support - Unity マニュアル  
https://docs.unity3d.com/ja/2023.2/Manual/AwaitSupport.html
________________________________________
## 1. 開始と更新の基礎
________________________________________
### 1.1. 基礎メソッド

メソッド     |概要
-------------|---------------------------------------------------
Awake()      |オブジェクト初回アクティブ時のみ。コンストラクタの代替
Start()      |オブジェクト初回アクティブ時のみ
Update()     |毎フレーム
LateUpdate() |毎フレーム（全てのUpdate()より後）
FixedUpdate()|一定の時間間隔毎。物理演算やRigidbody等と対となる
OnEnable()   |アクティブに変わる時
OnDisable()  |インアクティブに変わる時
OnDestroy()  |ゲームオブジェクトが破棄される直前。デストラクタの代替

.

Awake、Start、フレームの詳細

- InstantiateはUnity独自シリアライズ&デシリアライズしている
    - UnityEngine.Object継承クラスのインスタンスは参照が保持・復元される
    - 浅いコピーのような挙動
- Initialization中（※1）にInstantiateされたオブジェクトは、同フレーム中にStartとUpdateする
- Game Logic中（※2）にInstantiateされたオブジェクトは、同フレーム中にStartだけする
- アクティブでInstantiateする時、AwakeはInstantiate完了前に呼ばれる
- アクティブでInstantiateした後、StartはInstantiateの呼び出し元祖先を辿った次のいずれかが戻った後に呼ばれる
    - 基礎メソッド
    - コルーチン
- GameObjectではなくScriptを無効化した場合、Awakeは呼ばれるがStartは呼ばれない
- 1度もActiveにならなかった場合、OnDestroyは呼ばれない。（≒AwakeとOnDestroyは実質的に対）
- 「Script Execution Order」「DefaultExecutionOrder属性」でスクリプト間の実行順序が調整可能
    - 継承関係は考慮されず、設定したクラスのみに影響する
    - 飽くまでシーン内の実行順を制御する。複数のシーンがアクティブな場合はシーン間では不定

（※1）Scene初期化時の第1フレームのAwake、OnEnable、Start中
（※2）（※1）以外のほとんどの場合

Instantiateの挙動詳細

1. アタッチ順に従ってコンポーネントを初期化
    - コンポーネントのコンストラクタ
    - コンポーネントのインスペクタ読み込み
2. 全てのコンポーネントの初期化完了
3. BehaviourコンポーネントのAwake(GameObjectがアクティブな場合)
4. Instantiateが戻り値を返す
5. Instantiateを呼び出した呼び出し元の大元が終わるまで
6. BehaviourのStart(GameObjectがアクティブな場合)

________________________________________
### 1.2. コルーチン

※ 2018年のUnitaskの時点で、async/awaitが実用的だった
※ 2023以降は標準でもAwaitableでasync/awaitを積極的に対応したため、今はあまり使わない

機能                              |説明
----------------------------------|----------------
StartCoroutine(IEnumerator)       |コルーチンの開始
StopCoroutine(IEnumerator)        |コルーチンの停止
yield return new WaitForFixedUpdate()|次のFixedUpdate()後まで待つ
yield return null                 |次のUpdate()後まで待つ
yield return new WaitForSeconds(x)|Time.timeScaleに従うWait
yield return new WaitWhile(lambda)|条件を満たしている限りWait
yield return new WaitUntil(lambda)|条件を満たすまでWait
yield return new WaitForEndOfFrame() |このフレームの描画後の終わりまで待つ

.

コルーチンの補足

- Unityのコルーチンは、イテレータ/ジェネレータを転用した機能
- 開始時以外は、UpdateとLateUpdateの間に判定され再開されるものが多い。ただし以下を除く
    - WaitForFixedUpdate()
    - WaitForEndOfFrame()
- yield returnの転用である関係上、条件系をtrueで到達しても素通りせず1フレーム遅延する
- GameObjectが非アクティブになると動作を停止する
- コンポーネントがdisableになっても、動作は継続する
- コルーチンの終了部にyield return fooで、Currentから戻り値を得ることも可能

________________________________________
## 2. 衝突と物理演算
________________________________________
Collider

メソッド          |概要
------------------|--------------------------------
OnCollisionEnter()|Colliderの衝突開始時
OnCollisionStay() |Colliderの衝突継続中、毎フレーム
OnCollisionExit() |Colliderの衝突終了時
OnTriggerEnter()  |Triggerの衝突開始時
OnTriggerStay()   |Triggerの衝突継続中、毎フレーム
OnTriggerExit()   |Triggerの衝突終了時

.

Unity標準の物理演算の補足

- 完全に同じパラメータを与えても同じ結果にならない
- FixedUpdateを基準に動作する

________________________________________
## 3. Serializable、ScriptableObject
________________________________________
そもそもSerializableな型はどれか

Script serialization  
https://docs.unity3d.com/Manual/script-Serialization.html

1. 値型の組み込み型全般と、string型
2. 列挙型
3. Vector3、Quaternion、Color、Rect、Matrix4x4など、一部のUnity内の構造体やクラス
4. Serializable属性が付与された構造体やクラス
5. UnityEngine.Object継承クラス
6. 1～5の配列、List


Serializable

- publicフィールドはデフォルトでSerializable
- Serializableなフィールドの型がSerializable属性が付与された型の場合、自動でnewされる
- 非Serializableなフィールドの型がSerializable属性が付与された型の場合、何も起きない（nullのまま）

ScriptableObject

1. ScriptableObjectはAssetファイル化できる
2. 複数から同一のAssetファイルを設定した場合、同一のインスタンス参照となる
3. Assetファイル化されたScriptableObject内のSerializableなフィールドは、ファイル内に更新保存される
    - この挙動はUnityEditor上で実行している場合のみ
    - ビルドされたアプリは、Assetファイルが更新保存されることはない
        - 言い換えると、Saveデータの保存先には成りえない
4. Assetファイル化されたScriptableObject内のメンバとデバッグ実行はかなり変則的な挙動になる
    - privateメンバや自動プロパティについて
        - インスペクタからのリセット対象外
        - デバッグ開始&終了を跨いでも値が持続してしまう
        - シリアライズ対象外であるため、Unity Editorの終了&起動すると当然値は消える
    - NonSerializedを明示的に指定している場合
        - デバッグ開始時にリセットされる。デバッグ開始&終了を跨がない
    - Serializable属性が付与された型のフィールドについて
        - なぜか初期化され、nullでない状態になる

詳細不明、予想

1. Unity Editorはデバッグ終了&実行しても、Assetファイルの保存や再読み込みしていない
    - インスタンスを流用している
    - その結果、privateメンバの状態が跨いで持続してしまう
2. ただし明示的にNonSerializedが指定された場合は、デバッグ実行時にDefault値でリセットしている
3. Assetファイルから復元する際、Serializable属性が付与された型のフィールドに対して、privateかどうか見ていない

________________________________________
## 4. シーン関連
________________________________________
シーン追加 vs DontDestoryOnLoad

- SceneManager.LoadScene("AnotherScene", LoadSceneMode.Additive)
    - 元のシーンを保持したままシーンをロードする
- DontDestroyOnLoad()
    - 特定のGameObjectを特殊なDontDestroyOnLoadシーンに移動する
- シーン間のインスタンスの引継ぎは、可能な限り避けた方が良い
    - テストやデバッグの観点から、管理方法は考慮するとよい