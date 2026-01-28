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
### 1.1. Unityイベント関数

前提：これらは原則として、protected virtualにすること

メソッド     |概要
-------------|---------------------------------------------------
Awake()      |オブジェクト初回アクティブ時のみ。コンストラクタの代替
Start()      |オブジェクト初回アクティブ時のみ
FixedUpdate()|一定の時間間隔毎。物理演算やRigidbody等と対となる
Update()     |毎フレーム
LateUpdate() |毎フレーム（全てのUpdate()より後）
OnEnable()   |アクティブに変わる時
OnDisable()  |インアクティブに変わる時
OnDestroy()  |ゲームオブジェクトが破棄される直前。デストラクタの代替
Reset()      |Editor上で初めてアタッチしたときや、リセットした際

完全な一覧はこちらのMessages  
https://docs.unity3d.com/6000.0/Documentation/ScriptReference/MonoBehaviour.html

.

Awake、Start、フレームの詳細

- Instantiateの本質はUnity独自シリアライズ&デシリアライズ
    - UnityEngine.Object継承クラスのインスタンスは参照が保持・復元される
    - 浅いコピーのような挙動
- Initialization中（※1）にInstantiateされたオブジェクトは、同フレーム中にStartとUpdateする
- Game Logic中（※2）にInstantiateされたオブジェクトは、同フレーム中にStartだけ呼ばれ、Updateは呼ばれない（※3）
- アクティブでInstantiateする時、AwakeはInstantiate完了前に呼ばれる
- アクティブでInstantiateした後、StartはInstantiateの呼び出し元の祖先を辿った次のいずれかが戻った後に呼ばれる（※4）
    - Unityイベント関数
    - コルーチン
- GameObjectではなくScriptを無効化した場合、Awakeは呼ばれるがStartは呼ばれない
- 1度もActiveにならなかった場合、OnDestroyは呼ばれない。（≒AwakeとOnDestroyは実質的に対）
- 「Script Execution Order」「DefaultExecutionOrder属性」でスクリプト間の実行順序が調整可能
    - 継承関係は考慮されず、設定したクラスのみに影響する
    - **[重要]** 複数のシーンがアクティブな場合において、シーン間では考慮されず、シーン内で完結する
        - Unity6にてようやく公式で明確に言及され、不定であると明記された
    - これは最後の手段で、Awakeは自身の初期化を行い、Startで他の参照とのやり取りを開始するのが推奨プラクティスである

（※1） Scene初期化時の第1フレームのAwake、OnEnable、Start中
（※2） （※1）以外のほとんどの場合
（※3） 呼び出し元の祖先がUpdateだった場合、LateUpdateはそのフレーム中に呼ばれる
（※4） 厳密にはちょっと違う。現在実行中の player loop のタスク完了後のような表現がより正しい

Instantiateの挙動詳細

1. 各コンポーネントを初期化 (アタッチ順に従うことが多いが、非決定的)
    - 再帰的にインスペクタにアサインされた参照先の初期化も含む
    - 各コンポーネントをコンストラクタして確保
    - 各コンポーネントのインスペクタを復元
2. 再帰先含めて全てのコンポーネントの初期化完了
3. BehaviourコンポーネントのAwake(GameObjectがアクティブな場合) ※ 再帰先との呼び出し順序は非決定的
    - Awakeが呼ばれるものについては、OnEnableもその直後に呼ばれる
4. Instantiateが戻り値を返す
5. Instantiateを呼び出した呼び出し元の大元が終わるまで
6. BehaviourのStart(GameObjectがアクティブな場合)(Start, Update, LateUpdateの関係は前述の通り)

________________________________________
### 1.2. コルーチン

※ 2018年のUnitaskの時点で、async/awaitが実用的になった
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
### 1.3. Destroyとfake null

Destroyの基礎

- Destroy対象がgameObjectの場合
    - Transformを辿りGameObjectとアタッチされたComponentをDestroyする
    - fieldやプロパティにアサインされたComponentはDestroyされない
- Destroy対象がGameObject以外の場合
    - そのオブジェクトだけDestroyされる

UnityEngine.Object継承クラスとDestroyの注意点 （fake null問題）

- !=、==は、null扱い
- null合体演算子は、null扱いにならない
- null条件演算子は、null扱いにならない
- なお、否定演算子!もオーバーロードがあり、こちらでもDestroyチェック可能

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
    - 実行時にはAssetは直接利用せずPrototypeとして使い、Instantiateするのが定番
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
    - Serializableな型がScriptableObjectのメンバフィールドとして宣言されている場合
        - なぜか初期化され、nullでない状態になる
5. MonoBehaviourやScriptableObjectおよびそれらのメンバフィールドは、デフォルトコンストラクタが特殊なタイミング（メインスレッド以外）で呼ばれる
    - https://discussions.unity.com/t/scriptableobject-constructor-changed/169112?utm_source=chatgpt.com
    - **[重要]** 処々の問題が発生しうるため、そもそもMonoBehaviour、ScriptableObject、Serializableクラスではコンストラクタは避けるべきである
6. デシリアライズやOnAfterDeserializeは、DefaultExecutionOrderなどの対象外である。シリアライズのカスタマイズのためにのみ、使用する
    - https://discussions.unity.com/t/onafterdeserialize-not-work-on-scriptableobject-with-iserializationcallbackreceiver/878844
    - 上記はOnAfterDeserializeで初期化等を行おうとしているのが誤り
    - OnEnableとDefaultExecutionOrderなどを併用すれば、初期化済み順序は保証できる
        - AwakeはEditor上での挙動がプロジェクトを開いた時のみ実施される妙な挙動なため、避けたほうが無難
    - **[エッジケース]** 非ScriptableObjectでSerializableなクラスでは大きな問題になりうる
        - AwakeやOnEnableが使えないため、初期化タイミングをクラス自身で決定するのが困難


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

________________________________________
## 5. 実機で機能しないコード例
________________________________________
IL2CPP limitations  
https://docs.unity3d.com/6000.0/Documentation/Manual/scripting-restrictions.html

IL2CPP & AOTの制限で機能しないもの

- Strippingされてしまったもの
    - 未使用型などへのReflection、Serialization関連
- 例外フィルター (when)
- 実質的な動的IL(DLR)の要求
    - 登場していないジェネリック具象型
    - ExpandoObject, IDynamicMetaObjectProvider など
    - 動的コード生成全般：System.Reflection.Emit
- dynamic 
- Windows 固有機能への依存
    - System.Diagnostics.Process.Start
    - System.Management
    - Microsoft.Win32.Registry
    - System.Runtime.InteropServices など

その他

- 許可されているパス以外へのアクセス
- UIスレッド違反