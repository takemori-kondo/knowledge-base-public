# unity12. NavMesh
________________________________________
## 1. 準備

- AI Navigationパッケージを追加

※ AIとは無関係。NavMesh(経路探索ツール)

用途例

- CPUなどの経路探索
- RPGでのプレイヤーの移動範囲の制御などを移譲

________________________________________
## 2. 使い方

1. 移動可能範囲の設定(NavMeshベイク)
    - 移動範囲を配下に持つ空オブジェクトにNavMeshSurfeceをアタッチ
        - Collect Object : CurrentObjectHierarchy
        - その配下にCube等を追加していく
    - Bake
2. 自律移動者にNavMeshAgentをアタッチして、調整
    - SetDestination() などで目的地を指定

※ 移動する床など動的に移動可能範囲が変わる場合、以下のような対応が必要

- NavMeshObstacle
- Carving
- NavMeshのRuntime更新

________________________________________
## 3. プレイヤーの移動可能範囲に転用する場合

NavMeshAgentは「自動経路探索」ではなく、NavMeshから外に出ない制限として転用できる

1. 通常通りNavMeshベイク
2. プレイヤーにNavMeshAgentの追加
3. インスペクタ内の値を設定
    - Steering関連を0
    - Obstacle Avoidance関連を0やNone
4. 移動操作に対して直接移動処理する代わりに、NavMeshAgent.Moveあるいはvelocityに渡す