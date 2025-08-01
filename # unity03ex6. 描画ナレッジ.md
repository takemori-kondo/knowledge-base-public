# unity03ex6. 描画ナレッジ
________________________________________
## 1. Unity描画仕様の補足
________________________________________
### 1.1. UnityのBatching

ドローコールのバッチ処理 - Unity マニュアル
https://docs.unity3d.com/ja/2022.3/Manual/DrawCallBatching.html

Batching関連の一般概念や一般用語は、「2.1. 描画用語」を参照の事

________________________________________
### 1.2. UnityのLinear色空間とAlphaBlend

Linear色空間では、なぜかAlphaBlendに余計なガンマ補正が入る

- 画像編集ソフト上と同じ値にしても差が出る
    - 半透明の画像を重ねる場合は注意
- 通常より輝度が高くなる
    - 傾向としては暗い状態の幅が少なく極端になる

________________________________________
### 1.3. Screen space - Camera と ポストプロセス

Screen space - Cameraはポストプロセスの影響を受ける

- Tonemappingによる余計な補正が入る
    - 真っ白な表示が207や208程度のグレーまで暗くなる

________________________________________
## 2. 一般的な描画の知識とUnityでの状況
________________________________________
### 2.1. 描画用語

Draw Call

- Draw Callは描画の最小単位。CPUからGPUへ描画指示を出す単位
- 例えばDirect3D(=PC向けビルド)ならば、1回のpMesh->DrawSubsetなど
    - スプライトはそもそもID3DXSpriteがヘルパ
        - 実際にはpSprite->Drawの時点はDraw Callしていない
        - pSprite->End()にて、Begin()～End()間で使用されたTexture等でDraw Callが決まる
- 例えばMetal(=iOS向けビルド)ならば、1回のdrawIndexedPrimitivesなど
- これらの低レベルAPIは、基本的にSprite関連を除けばバッチング機能は内蔵していない

Set Pass Call

- Draw Callをひとまとめにできない際のパラメータ切り替え処理
- CPU側で行う処理で、コストが高い
- Unityにおいては以下
    - 「シェーダ/テクスチャ/マテリアル/メッシュ/GPU State」いずれかが異なる際の切り替え処理
    - ただしSRP Batcherでは、シェーダバリアントさえ同じなら1つのSet Pass Callで処理できる

Batching

- 描画コストを削減するためのアプローチ
- Unityでは3つの異なるアプローチが実現されている
    - 1つ目は、Set Pass Callを減らすように描画順を並べ替える
    - 2つ目は、位置だけ異なり他が同一の頂点バッファ配列同士を、予め1つにまとめる
        - 単一のDraw Callにしてしまおうという発想
    - 3つ目は、Spriteヘルパの仕組みそのもの。Sprite Batching

Sprite

- 2Dを描画する仕組みまたはヘルパ、あるいはそれによって描画される1つ1つの画像の事
- 例えばDirect3Dでは、2Dを描画するためのヘルパとしてSprite関連のAPIがある
- Unityにおいては、2D画像に特定の設定をしたAssetの事もSpriteと呼ぶ

Sprite Atlas

- CG技術の古典からある手法
- 複数の画像を単一のテクスチャで用意すれば単一のDraw Callにできる、というもの
- 例えばDirect3DのSpriteヘルパは、この手法を内蔵している

________________________________________
### 2.2. 3Dの基礎

UnityのCPU側の事前処理

1. Static Occlusion Culling
2. 前フレームのGPU計算結果を活用したDynamic Occlusion Culling 

フォワードレンダリングのパイプライン

1. プリミティブアセンブリ
2. 頂点シェーダ
    1. ワールド変換
    2. ビュー変換（カメラ変換）
    3. プロジェクション変換（視錐台/frustum変換。同次クリップ空間化）
    4. 正規化（NDC変換）
3. ジオメトリシェーダ
4. クリッピング
5. ラスタライズ等
    1. スクリーンマッピング（ビューポート変換）
    2. 三角形のセットアップ
    3. 三角形トラバーサル
    4. 早期Zテスト・ステンシルテスト・アルファテスト ※ GPUが対応している場合
6. ピクセルシェーダ（フラグメントシェーダ）
7. マージ/ROP
    1. Zテスト・ステンシルテスト・アルファテスト ※ GPUが早期テストに対応していない場合
    2. Zバッファ更新、ステンシルバッファ更新、ブレンド
8. フレームバッファ
9. ポストプロセス

URPがカスタマイズ可能な部分の例

1. 根本的なレンダリング順のソート（RenderQueueの変更、SRPでの制御）
2. ラスタライズ周辺の処理
    1. フラグメントシェーダの切替
    2. Zバッファ、ステンシルバッファの切替（アウトライン処理や半透明処理の専用化など）
3. ポストプロセスの制御
4. カメラごとに上記を切替
5. Zプリパスの活用
6. Gバッファ的な活用

※ Unityにおいては、ピクセルシェーダ≒フラグメントシェーダであり、用語の意味に差はほぼない