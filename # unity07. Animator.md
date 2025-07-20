# unity07. Animator
________________________________________
## 1. Animator
________________________________________
アニメーション遷移 - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/class-Transition.html
________________________________________
### 1.1. 基礎知識

構成要素

1. Animator コンポーネント
2. Animator Controller
3. Animation Clip
4. 制御されるコンポーネント

Animator・Timeline共通の注意点

- AnimatorやTimelineは、Prefabモードでは設定不能な操作が存在する
    - Prefabモードの制限に抵触する操作があるため
- そのため、Prefabモードで無理に調整を行うのではなく、Prefabを仮のシーンに配置して調整&overrideするのが正攻法

________________________________________
### 1.2. 基本的なプラクティス

方針A：Animator ControllerはPlayで指定する内容を束ねる単位として利用する

- シンプルな場合はこちらの方が楽

方針B：Animator Controllerで状態・遷移を適切に管理して、トリガーで制御する

- 中間状態などを自動的に制御したい場合はこちらになる。複雑になりがち

________________________________________
### 1.2. Animation Clipの設定

プロパティ         |概要
-------------------|---------------------
Loop Time          |ループするかどうか

.

________________________________________
### 1.3. Animator Controllerの設定

手順

1. Animator Controllerを用意
2. 状態、遷移、パラメータを追加
    - 状態にClipを設定
    - 各遷移にプロパティや条件を設定

特殊なノード

ノード   |役割
---------|-------------
Entry    |アニメーションを開始する時は、Entryノードから遷移する
Any State|現在の状態にかかわらず、特定のアニメーションに遷移したい場合に使う
Exit     |アニメーションを終了したい場合は、Exitノードに遷移する

.

遷移のプロパティ

プロパティ         |概要
-------------------|---------------------
HasExitTime        |アニメーションが特定の位置になるまで遷移開始を遅らせるか
ExitTime           |HasExitTimeがOnの時の具体的な位置
Fixed Duration     |TransitionDurationに指定した値を秒として解釈するか
TransitionDuration |遷移元・先のアニメをミックスする時間
TransitionOffset   |遷移先アニメをはじめ以外から再生開始するか
Interruption Source|遷移中に別の遷移条件が発生した場合、割り込ませるか
Conditions         |遷移条件。Triggerが発生した、など

.

Current、Next、Interruption Source

- TransitionDurationがある場合、遷移元がCurrent、遷移先がNextである
- Interruption SourceをCurrentにすると、Currentを遷移元とする遷移の割り込みが許可される
- Interruption SourceをNextにすると、Nextを遷移元とする遷移の割り込みが許可される

別件

- ButtonあるいはToggleから自動生成すると、ひとまとめの専用アニメーションassetが作れる

________________________________________
### 1.4. トリガーによる制御

トリガーを発生させる

- SetTrigger()

現在・次のステートを取得する

- GetCurrentAnimatorStateInfo()
- GetNextAnimatorStateInfo()