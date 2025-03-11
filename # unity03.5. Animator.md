# unity03.5. Animator
________________________________________
## 1. Animator
________________________________________
アニメーション遷移 - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/class-Transition.html

________________________________________
### 1.1. 設定

手順

1. Animator Controllerを用意
2. 状態、遷移、パラメータを追加
    - 状態にClipを設定
    - 各遷移にプロパティや条件を設定

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
### 1.2. 制御

トリガーを発生させる

- SetTrigger()

現在・次のステートを取得する

- GetCurrentAnimatorStateInfo()
- GetNextAnimatorStateInfo()