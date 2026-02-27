# unity03ex5. 3D と Unity UI の統合
________________________________________
## 1. 前提知識
________________________________________
### 1.1. unity03. 3D・Camera・Renderer の要点

#### 1. Unity3Dの基礎知識

- スクリーン座標基礎 にて 画面とWorldの行き来は可能
- Layerによって、カメラ毎の撮影対象の絞り込みが可能
- SortingLayerによって、以下の描画間の前後関係が調整可能
    - Screen Space - Camera
    - SpriteRenderer
    - ParticleSystem

#### 2. Camera (URPの場合)

- マルチカメラの「DepthOnly または stack」で、Zをリセットしたカメラ単位の上書きが可能
- 以下の理由により、UI撮影用のカメラは分けることになりがち
    - UIとParticleSystemを、RenderTexture等を使わずに簡単に前後制御したい
    - Screen Space - Camera は、PostProcessの影響を受けるが、受けたくない
    - UIやUIエフェクトは、メイン描画より常に前面に出てほしい

________________________________________
### 1.2. unity03ex1. Sprite、Sprite Renderer の要点

#### 2. Sprite Renderer

- 2.1. FHD Perspectiveピクセル等倍距離まとめ
- 2.2. SpriteRendererをビルボードしたい場合

________________________________________
### 1.3. unity03ex4. Unity UI の要点

#### 1. UIの基本的なプラクティス

- パフォーマンス大原則
- 制作効率上の原則
- Screen space - Camera と ポストプロセスの嫌な関係

#### 2. Canvasの基礎知識

- Canvas Scalerコンポーネント

________________________________________
### 1.4. その他 の要点

1. Screen space - CameraのRectTransform配下にTransformは普通にぶら下がる
    - 子ノードは素直にTransformの影響を受ける
    - Canvas Scalerで制御されたCanvasノードのscaleは、Distanceで決まる
    - 「FHD Perspectiveピクセル等倍距離まとめ」の通りに指定しておくと、scale = 0.01になる
2. 上記TransformにさらにRectTransformをぶら下げることも可能
    - WorldSpaceでぶら下がったような挙動になる

________________________________________
## 2. 妥協アプローチ
________________________________________
### 2.1. 前提条件

1. HD, FHDなど、16:9比率のみが対象で、スマホのフリーサイズを想定しない
2. SpriteRendererは、UI空間に事実上配置したい
3. ScreenSpace - Camera を使う

________________________________________
### 2.2. アプローチ詳細

内容

1. MainとUIカメラを典型手法通りに別々にして撮影し、カメラ間の描画前後関係を保証する
2. 予め、CanvasのDistanceは、「2.1. FHD Perspectiveピクセル等倍距離まとめ」の通りにしておく
3. SpriteRendererを含むPrefabから作られたインスタンスは、Canvasに直接ぶら下げる
    - デザイナーには、上記によって期待した表示になるようにパーツを作ってもらう

結果

- Canvasにぶら下がったTransformは、CanvasのRotationやScaleの影響下になる
    - Rotationはカメラとの位置関係で自動計算された結果の値になる
    - ScaleはCanvas Scalerで自動計算された結果の値になる
    - 実質的にUI空間にいるような状態になる

考慮事項

- 動的にカメラFovが変わる場合、必要に応じてDistanceの再計算が必要
- SpriteRendererは、Transform
    - pivotやAnchor機能があるわけではないので、その手の配置統合は出来ない