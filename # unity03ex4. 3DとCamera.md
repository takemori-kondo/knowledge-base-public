# unity03ex4. 3DとCamera
________________________________________
## 1. Unity3Dの基礎知識
________________________________________
### 1.1. 座標系とレイヤー

座標系

1. スクリーン座標：左下を(0,0)とするスクリーンの座標
2. ワールド座標：3D空間上の座標
    - Rotate（オイラー）回転の向きは、Z=X&rarr;Y、Y=Z&rarr;X、X=Y&rarr;Z
    - Rotate（オイラー）回転の順序は、Z、X、Y
3. UnityEngine.Input.mousePosition
    - スクリーン座標を取得
4. Camera.ScreenToWorldPoint()
    - スクリーン座標をワールド座標に変換する。z距離は引数で与える

Layer

- 3Dオブジェクトのグループ単位。ライティング、衝突、描画の一括制御する

________________________________________
### 1.2. レンダリング手法とURP-Volume

レンダリング手法の例

1. Vertex Lit。フラットシェーディングのような計算(PS相当)
2. Forward。フォンシェーディング&法線マップ。反射光は考慮しない(PS3相当)
    - 最も標準的なレンダリングパイプライン。Unityのデフォルト
3. Deferred。G-Buffer(ライティング計算用のバッファ群)を用意し、全てのライティングを1度に計算
    - G-Bufferはz情報がないため、半透明が表現できない

URP-Volume

- 既定の設定では、Unityのポストプロセスの一部

________________________________________
## 2. Camera
________________________________________

Render Type

- Baseカメラは従来のカメラ
- Overlayカメラは他のカメラのStackに追加する
- Overlayカメラは画面全体Render Textureとやれることは同じ

Renderer（ライティング計算手法や順序の設定）

|プロパティ       |意味
|-----------------|----------------------------
|Renderer         |URPにおけるレンダリングパスの指定
|Stop NaNs        |NaNを黒扱いで続行するか
|Dithering        |カラーディザリング処理するか
|Priority         |複数カメラがある場合の、カメラ間の描画順序
|Opaque Texture   |シェーダで_CameraOpaqueTextureを利用可能にするか
|Depth Texture    |シェーダで_CameraDepthTextureを利用可能にするか
|Culling Mask     |描画対象にするレイヤーの選択。Everythingは全て
|Occlusion Culling|カメラの方を向いていないポリゴン描画を省略するか

.

Environment（初期化方法やURP-Volumeの設定）

|プロパティ             |意味
|-----------------------|----------------------------
|Background Type        |旧名称：Clear Flags。描画内容の初期化方法
|Volumes - Update Mode  |初期化タイミングの指定
|Volumes - Volume Mask  |Volumeが影響するレイヤーの選択
|Volumes -Volume Trigger|Volumeを適用するかどうかのトリガーの設定

.

Output（出力先やHDR、MSAAの設定）

|プロパティ       |意味
|-----------------|----------------------------
|Output Texture   |テクスチャに出力する場合の設定
|HDR              |明るすぎる場面や暗すぎる場面でも色飛びを抑えるかどうか
|MSAA             |ピクセル化の際に複数の点から最終的な色を決定する仕組み。シャギーが軽減される

.

________________________________________
## 3. 複数Cameraの典型的な設定
________________________________________
### 3.1. UI Camera

用途

- Main : UI以外を撮影する。Baseにする
- UI   : UIを撮影する。OverlayにしてMainにスタックする

典型的な設定

カメラ|Clear      |Culling Mask|Depth|Target       |cinemachine
------|-----------|------------|-----|-------------|-----------
Main  |SkyBox     |UI以外      |-1   |None         |-
UI    |-          |UI          |-    |-            |-

________________________________________
### 3.2. 複数視点の例

用途

- Main : 全体を描画。Baseにする
- Sub  : 顔アップなどを描画。Baseにする
- UI   : UIを撮影する。OverlayにしてMainにスタックする

典型的な設定

カメラ|Clear      |Culling Mask|Depth|Target       |cinemachine
------|-----------|------------|-----|-------------|-----------
Main  |SkyBox     |UI以外      |-1   |None         |-
Sub   |Solid Color|UI以外      |0    |RenderTexture|Brain
UI    |-          |UI          |-    |-            |-

.

