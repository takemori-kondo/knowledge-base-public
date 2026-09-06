# unity03ex1. Sprite、Sprite Renderer
________________________________________
## 1. Spriteの基本設定
________________________________________
Sprite エディター - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/SpriteEditor.html

スプライトアトラス V2 - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/SpriteAtlasV2.html
________________________________________
### 1.1. 設定やPackage追加

1. Windows > Package Manager > Unity Registry > 2D Sprite
2. Edit > Project Settings > Editor > Sprite Atlas > Mode > V2 Enabled

________________________________________
### 1.2. Sprite Atlasの設定

Create > 2D > Sprite Atlas

- Type : Master
- Scriptable Packer : None
- Include in Build : On
- Packing
    - Allow Rotation : Off
    - Tight Packing : Off
    - Alpha Dilation : Off
    - Padding : 4
- Texture
    - Read/Write : Off
    - Generate Mip Maps : Off
    - sRGB : On
    - Filter Mode : Bilinear
    - Show Platform Settings For : Main Texture
- Default
    - Max Texture Size : 2048
    - Format : Automatic
    - Compression : None （特にドット画像をまとめている場合）
- Objects for Packing
    - 含めたい内容を追加していく （フォルダごとD&Dなども可能）

※ Atlasを設定してもしばらく反映されない場合がある。Atlasプレビューや再ビルドなど試すべし  

Compression設定補足

- 個別とAtlasで2段階行われる。個別画像はNoneが推奨
- また、ドット画像の場合はAtlasもNone推奨

________________________________________
### 1.3. 個々のSpriteの典型的な設定

通常

1. Texture Type > Sprite
2. Sprite Mode > Single
3. MeshType > Tight

9スライス

1. Texture Type > Sprite
2. Sprite Mode > Single
3. MeshType > FullRect

※ 描画の最適化はAtlasで解決できるため、特別な理由がなければMultipleは不要

スプライトシート・スライス

1. Texture Type > Sprite
2. Sprite Mode > Multiple
3. Mesh Type > FullRect
4. Filter Mode > Point(no filter)
5. Sprite EditorでGrid By Cell Sizeでスライス
6. 使用したいセルを複数選択してシーンにドラッグすればanimファイルになる
7. AnimationController側はStateを使わず、BlendTreeで処理するのが鉄板

※ スプライトシート・スライス方式がAtlasと併用して恩恵受けられるか未検証

________________________________________
### 1.4. 9スライスの用途

典型的な用途

- 円画像を用意して、丸角の枠にする
- 枠画像を用意して、メッセージウインドウの枠にする

________________________________________
### 1.5. 個々のスプライトにボーン・スキンを設定

公式パッケージ「2D Animation」 の追加が必要

- テクスチャのインスペクタに「Open Sprite Editor」が追加される

________________________________________
### 1.6. タイルマップ

公式パッケージ「2D Tilemap Editor」「2D Tilemap Extras」の追加が必要

- Tile Palleteウィンドウが追加される

________________________________________
## 2. Sprite Renderer
________________________________________
### 2.1. FHD Perspectiveピクセル等倍距離まとめ

v-Fov = 2 × Atan(縦解像度 / (2 × PPU × 距離))

距離 = 縦解像度 / (2 × PPU × Tan(vFov / 2)) 

Ratio, v-Fov|距離, PPU         |A    |B
------------|------------------|-----|--------
16:9, 60    | 9.353075, PPU=100|20mm |61.927度
16;9, 45    |13.036754, PPU=100|28mm |46.397度
16:9, 10    |61.722283, PPU=100|135mm|10.159度

※1 A=現実のフルサイズカメラでの似た設定の焦点距離  
※2 B=その時の垂直画角（対角画角ではない）

________________________________________
### 2.2. SpriteRendererをビルボードしたい場合

カメラの方を向くようにしたい場合(球面ビルボード)

```cs
Vector3 toCam = cam.transform.position - this.transform.position;
if (toCam != Vector3.zero)
{
    // ワールドYは固定したい場合の追加処理
    // toCam.y = 0f;
    this.transform.rotation = Quaternion.LookRotation(toCam, Vector3.up);
}
```

カメラと平行にしたい場合(スクリーンビルボード)

```cs
this.transform.forward = this.sourceCamera.transform.forward;
// ワールドYは固定したい場合の追加処理
// var fwd = this.sourceCamera.transform.forward;
// fwd.y = 0f;
// if (fwd != Vector3.zero)
// {
//     this.transform.rotation = Quaternion.LookRotation(fwd, Vector3.up);
// }
```

________________________________________
### 2.3. Sprite Rendererのその他のTips

1. Sorting Groupコンポーネントで、描画順をグルーピング可能
2. Sprite Maskコンポーネントで、2値画像ベースのマスクが可能

Sprite Maskの活用例1：パーツ化された各半透明スプライトが多重表示にならないように

- 表示Spriteと同じ内容でSprite Maskすれば、Spriteが多重に重ならなくなる
- Custom Range、AlphaCutOffで調整すればだいたい良い感じになる

Sprite Maskの活用例2：レトロ点滅透明・格子透明など

描画量調整用の画像とSprite Mask・AlphaCutOffでレトロ擬似透明するのもあり

________________________________________
## 3. IK
________________________________________

1. 2D Animation 追加
2. Sprite Editor > Skining Editor
3. CreateBone、Auto Geometry、Auto Weight を それぞれ Apply や Generate
4. SpriteRenderer + Sprite Skin追加、CreateBone押下
5. 同ノードにIK Manager 2Dを追加し、+押していずれかのSolverも追加
6. Solver設定。Effectorに末端ノード、ChainLengthに影響したい分だけ設定