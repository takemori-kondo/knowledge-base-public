# unity03. 3D・Camera・Renderer
________________________________________
## 1. Unity3Dの基礎知識
________________________________________
### 1.1. 座標系・レイヤー

Transform基礎

項目              |内容
------------------|--------------
Z回転の+方向      |X→Y
Y回転の+方向      |Z→X
X回転の+方向      |Y→Z
Rotateの順序      |Z、X、Y
Transform.forward |ローカル+z方向

.

スクリーン座標基礎

項目                |内容
--------------------|-----------------
原点(0,0)           |左下
マウスの位置        |UnityEngine.Input.mousePosition
World座標へ変換     |Camera.ScreenToWorldPoint(position)
スクリーン座標へ変換|Camera.WorldToScreenPoint(position)
特定の座標系への変換|RectTransformUtility.ScreenPointToLocalPointInRectangle(...)

.

Layer

- 3Dオブジェクトのグループ単位
- 撮影判定、ライティング、衝突判定の一括制御できる

________________________________________
### 1.2. レンダリング手法・URP-Volume

レンダリング手法の例

項目      |内容
----------|-----------------
Vertex Lit|初代PSライク
Forward   |普通。フォンシェーディング&法線マップ。反射光は考慮しない
Deferred  |G-Bufferを用意し、全てのライティングを1度に計算

.

URP-Volume

- 既定の設定では、Unityのポストプロセスの一部

________________________________________
### 1.3 描画順序・前後関係・Camera

endering order in the Built-in Render Pipeline  
https://docs.unity3d.com/2022.3/Documentation/Manual/built-in-rendering-order.html

Unity＋スマホで3Dゲーム開発最適化するための考え方  
https://speakerdeck.com/cygames/unity-sumahode3dgemukai-fa-zui-shi-hua-surutamefalsekao-efang?slide=54

Controlling Unity's rendering order - why and how | Toca Boca Tech Blog  
https://medium.com/toca-boca-tech-blog/controlling-unitys-rendering-order-why-and-how-3c2bb1da8e78

要点

1. 描画順序はRenderQueueで決まる。デフォルトは以下4つ
    - 不透明(2000)
    - SkyBox(実質2500.5。特殊ルール)
    - 半透明(3000)
    - Overlay(4000)
2. 前後関係はZテストがある
3. SortingLayerは、2501以上の同一のRenderQueue内でのソートを実現している
    - 不透明MaterialはRenderQueueが2000。SortingLayerで管理できない
4. 複数カメラがある場合、カメラごとに完結した描画が行われる
    - 例えば以下のいずれかの方法で、既存の描画結果に上書き可能
        - ClearFlag=DepthOnlyで後から描画 (Built-in時代の定番)
        - Camera StackでStackされたCamera (URP時代の定番)
5. 撮影対象かどうか自体の選定には、カリング・Layerがある
6. ScreenSpace - Overlayはユーザが制御できるCameraとは無関係に最前面に描画される

実質的な優先度のまとめ

1. Screen Space - Overlay & Sort Order
2. CameraのPriority (Camera.depth)
3. Camera stack
4. SurfaceType=OpaqueなMaterialの内容の、距離
    - 厳密には、自動ソートしてくれるパイプラインの場合のみ
5. それ以外の、SortingLayer (sortingLayerName) & Order in Layer (sortingOrder)
    - Screen Space - CameraなCanvas
    - SpriteRenderer
    - ParticleSystemRenderer
    - SurfaceType=TransparentなMaterialのMeshRenderer など
6. 5が同一な場合、距離
7. 6が同一な場合、UIならばヒエラルキー上の順番(後勝ち・子勝ち)
    - 非UIは非保証・ランダム

________________________________________
## 2. Camera (URPの場合)
________________________________________
### 2.1. 設定項目

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
### 2.2. URPでの複数Camera構成の典型例

各カメラの用途

- Main Camera : UI以外を撮影する。Baseにする
- UI Camera   : UIを撮影する。Mainにスタックする。Canvasは、Screen Space - Cameraにする
- Sub Camera  : UI以外を撮影する。Baseにする。RenderTextureに描画して自由に調整する

典型的な構成

カメラ     |Clear      |Culling Mask|Priority(Depth)|Target       |cinemachine
-----------|-----------|------------|---------------|-------------|-----------
Main Camera|SkyBox     |UI以外      |必要なら設定   |-            |-
UI Camera  |-          |UI          |-              |-            |-
Sub Camera |Solid Color|UI以外      |必要なら設定   |RenderTexture|Brain

.

この構成のメリット

- UI Cameraの撮影内容が扱いやすくなる
    - Main Cameraより描画が手前に来ることを保証する
    - UI CameraのPost Processをオフにし、UIの色計算がずれるのを防ぐ(Unity6.0時点)
        - ここに関しては、Unity公式が仕様を固定しないスタンスなので将来ずれる可能性あり
    - Screen Space - Cameraにより、Canvasとそれ以外の前後の調整を可能にする

※ Sub CameraがUIと干渉しないなら、RenderTextureではなくView Rectで解決することもできる

※ レガシーの場合はcameraスタックはない。Camera.depth & ClearFlag=DepthOnly、またはRenderTextureを活用して調整する

________________________________________
## 3. Renderer
________________________________________
Renderer基礎

- 描画対象は何らかしらのRendererを持つ。以下代表的なもの
    - MeshRenderer
    - SkinnedMeshRenderer
    - LineRenderer
    - TrailRenderer
    - SpriteRenderer
    - ParticleSystemRenderer
    - CanvasRenderer
    - TextMesh Proの内蔵(MeshRenderer or CanvasRenderer)
- Renderer.enabled = false で 非表示
