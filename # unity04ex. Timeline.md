# unity04ex. Timeline
________________________________________
## 1. Timeline
________________________________________
### 1.0. Animator・Timeline共通の注意点

- AnimatorやTimelineは、Prefabモードでは設定不能な操作が存在する
    - Prefabモードの制限に抵触する操作があるため
- そのため、Prefabモードで無理に調整を行うのではなく、Prefabを仮のシーンに配置して調整&overrideするのが正攻法

________________________________________
### 1.1. 概要

Animatorの豪華版のようなツール

構成物           |概要
-----------------|------------------------------------
.playable asset  |Timelineアセット
PlayableDirector |.playable asset再生のためのComponent



