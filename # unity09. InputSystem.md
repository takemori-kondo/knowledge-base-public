# unity09. InputSystem
________________________________________
Input System  
https://docs.unity3d.com/Packages/com.unity.inputsystem@1.18/manual/index.html

Migrating from the old Input Manager  
https://docs.unity3d.com/Packages/com.unity.inputsystem@1.18/manual/Migration.html

Timing and Latency  
https://docs.unity3d.com/Packages/com.unity.inputsystem@1.18/manual/timing-and-latency.html

________________________________________
## 1. 基礎情報
________________________________________
### 1.1. 概要

要約：マルチプラットフォーム前提かつキーアサイン機能内臓

全体設定

設定等              |内容
--------------------|-------------------------------------------------------------------------
InputManagerの無効  |Edit > Project Settings > Player > Other Settings > Active Input Handling
Input System Package|Project Settings > Input System Package

.

Input System Package

設定等              |内容
--------------------|-------------------------------------------------------------------------
Updateモードの設定  |Settings > Update Mode
┣ Dynamic Mode     |Updateの前。NewInputUpdate
┗ Fixed Update     |FixedUpdateの前。NewInputFixedUpdate
デッドゾーンの設定  |Binding Property の Processorsで追加(※1)
InOutの設定         |(※2)

(※1) つけなくても0.125、0.925にデフォルト設定される？
(※2) つけなくてもIn=0.5、Out=Inの75%にデフォルト設定される？

Unity6以降の初期用意済みのアクションの注意点

- Player/Moveをそのまま使うなら、色々プログラム側で制御が必要
    - 押した瞬間、離した瞬間を認識できる自前クラスを用意したほうが良い
        - Vector2は軸ごとの開始終了判定ができない
    - 斜め入力にならない角度制限も必要に応じて対応が必要
        - 弱い軸 < 強い軸 * Tan(θ) ならば、弱い軸を無視する  
        - θは概ね15度～22.5度
- UI/Navigateアクションのデフォルト設定は、実用しづらい
    - Pass Through & Padの両スティック設定で、Pad勝ち。使い勝手悪すぎ