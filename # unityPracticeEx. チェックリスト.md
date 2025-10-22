# unityPracticeEx. チェックリスト
________________________________________
## 1. Prefab、インスペクタ、Serialize関連
________________________________________
### 1.1. 設計の基礎

Instantiateとactive化の定番プラクティス

- https://docs.unity3d.com/6000.0/Documentation/ScriptReference/MonoBehaviour.Awake.html
- https://docs.unity3d.com/6000.0/Documentation/ScriptReference/Object.Instantiate.html
- Instantiateの結果が返ってきた時、それがactiveなら、そのオブジェクト階層のactiveなノードのスクリプトは全てのAwake、OnEnableが終わっていることが保証される
- UnityEvent全般は引数を取ることができないため、Instantiate後にSetupなどのパラメータを渡す機構を用意する事が良くある

MVC

```text
   Ｃ  ◁┐       Ｃ   
  ↙  ↘  │      ↗  ↘  
Ｍ ← Ｖ┘    Ｍ    Ｖ
└──▷               
```


実際のViewクラスのプラクティス

メンバや構造         |概要
---------------------|-----------------------------
Setup()              |初期化用の外部パラメータはここで渡す
Cleanup()            |-
Awake()              |階層内のUIコンポーネントとのバインドを行う
WindowState          |Closed, Showing, Shown, Closing
OperationResult      |本来の戻り値T、OperationResultStatus
OperationResultStatus|Accepted、RejectedDueToDuplicate、RejectedDueToCooldown
ShowAsync()          |閉じるまで完了しない。OperationResult T を戻り値とする
CloseAsync()         |外部からCloseを要求する場合に使う
IsFocused            |フォーカス・非フォーカスがある場合に作る
FocusAsync()         |フォーカス・非フォーカスがある場合に作る
UnfocusAsync()       |フォーカス・非フォーカスがある場合に作る
SetData()            |データを渡す場合にはこちら
OnXX                 |UnityEngine.UIに対するイベントハンドラがある場合、Controllerまで伝播させる。Viewロジックではないロジックは自分で処理しない

.

________________________________________
### 1.2. インスペクタと初期値

- Color等のUnityが対応する複合型はコンストラクタ初期化子で設定しても、初期値は0になってしまう
- エディターのReset時のみ呼ばれるReset()を用意しそちらで初期化すればよい

________________________________________
### 1.3. ScriptableObject・Serializableクラスで避けるべき事項

1. コンストラクタを避けよ。実行順を制御できない。Awakeを使え
2. 自動プロパティ、属性を指定しないprivateフィールドを避けよ。インスペクタでのリセットの挙動が変則的である
3. OnAfterDeserializeで初期化処理は避けよ。実行順を制御できない。Awakeを使え

________________________________________
## 2. スクリプト実行順関連

原則として、実行順序に依存する処理は避ける

1. シーン内の実行順は制御可能
2. シーン間の実行順は未定義
3. Updateより前に実行したい内容がある場合は、PlayerLoopをカスタマイズするのが確実