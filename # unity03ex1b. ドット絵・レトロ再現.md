# unity03ex1b. ドット絵・レトロ再現
________________________________________
## 1. 2D Pixel Perfect Camera
________________________________________
### 1.1. レトロ再現設定

Pixel Perfect Camera component reference for URP  
https://docs.unity3d.com/6000.0/Documentation/Manual/urp/2d-pixelperfect-ref.html

※ Unity6以降 + URP なら標準に含まれる。Orthographic用

1. RenderTexture用意
    - Filter Mode : Point
    - Anti-Aliasing : None
    - Mip Maps : Off
2. Cameraは256x224のRenderTextureに出力
3. Cameraと同じノードにPixel Perfect Cameraを追加
    - Assets PPU : 16
    - Reference Resolution : 256x224
    - Crop Frame : None
    - Grid Snapping : Upscale Render Texture
    - (Filter Mode : Point)
4. 全てのスプライト側に以下を同じ値で設定
    - PPU : 16
    - Filter Mode : Point (no filter)
    - Generate Mip Maps : Off
5. 上記RenderTextureをCRT再現用のシェーダ等に通して加工
    - NTSCピクセルアスペクト比補正(114%)
    - 走査線
    - 色にじみ
    - ガンマ特性

________________________________________
### 1.2. 補足

各再現解像度

対象|wh(実表示)|パレ|実質|実質タイル数|推奨PPU
----|----------|----|----|------------|-------
FC  |256x224   |  16|  16|       16x14|     16
SFC |256x224   |   8|  16|       16x14|     16
GBC |160x144   |   8|  16|        10x9|     16
GBA |240x160   |   8|  16|       15x10|     16

.

CRTの実際

- NTSC規格
    - 縦横比：4:3
    - 有効走査線：240本
    - 色副搬送波(fsc)：約3,579,545.4Hz
    - 水平走査(fh)：15,734Hz
        - 白黒規格では525×30 = 15,750Hzだったが、カラー時にfscを基準にしたため変更が入った
    - 1走査線全体(1/fh)：約63.5μs
    - 内、水平Blank(x)：10.9μs
    - 1走査の描画時間(t = 1/fh - x)：約52.6μs
- CRTの挙動
    - 480i59.94（フィールドレート59.94Hz, 1フィールド240本の2フィールドインターレース方式）
    - 240本を基準に、4:3の範囲に電子ビームを発射する
    - 発射範囲はオーバースキャン（通常は画面全体より少し大きい範囲へ発射）

SFC側の挙動

- 240本インターレース方式のまま位相ズレを意図的に廃してプログレッシブ同等の59.94Hzを実現
- オーバースキャンで実質画面外となる上下8本ずつは転送対象外とした、224本を転送対象
- 基準クロック：約21,477,272Hz（NTSC色副搬送波の6倍）
- 基準クロック4クロック分＝1画素の時間(o)：約0.186μs
- 4:3(320:240)で正方形pixelを出すための本来の時間(p = t/320)：0.164μs
- 1pixelのピクセルアスペクト比(o/p) : 約113.4%

4:3 NTSC CRTテレビ再現の要点

1. 一度RenderTextureに描画し、最終表示時に横方向へ引き延ばす
    - NTSC実機計算上は約113.4%だが、慣習的に約8/7倍(114%)を使う
2. 各種再現する
    - 走査線
    - 色にじみ
    - ガンマ特性

________________________________________
## 2. ベルトスクロール方式の自動ソート
________________________________________
Rendererアセットが2D Rendererなら調整可能

1. URPのProject Settings > Graphics > クリック
2. インスペクタ設定
    - Transparency Sort Mode > Custom Axis
    - (x, y, z) = (0, 1, 0)

詳細は以下参考のこと

https://tsubakit1.hateblo.jp/entry/2017/04/14/024945

________________________________________
## 3. ドット絵にライティング
________________________________________
【Unity】ドット絵に照明効果を追加する  
https://tsubakit1.hateblo.jp/entry/2018/06/08/220000

________________________________________
### 3.1. 光が届く範囲が見える

- 2値ならSpriteMask
- ライティングするならSpriteに専用シェーダを割り当てる
    - DiffuseSprite Material用意
        - シェーダはSprite > Diffuse
    - TileMapRenderer, SpriteRendererのMaterialに設定
    - ライトを配置

________________________________________
### 3.2. 光に向いてる面を明るく（ドット絵に法線付与）

1. ドット絵に対応する法線マップを用意
2. Standard Shaderなどをセットしたマテリアルを用意し、Normal、Emission設定
    - Rendering : Transparent
    - Normal Map : 用意した法線マップを設定
    - Emission : ON
        - Color : HDR
        - Global Illumination : Baked
    - 法線マップとEmissionマップは同一レイアウトである必要あり
        - Sprite Atlasとの併用が困難

________________________________________
### 3.3. ドット絵に対してシャドウもつける

1. 落ち影生成用の3Dモデルを配置する
2. MeshRendererのCastShadowをShadowOnlyにする

※ Tilemapとの併用はできない