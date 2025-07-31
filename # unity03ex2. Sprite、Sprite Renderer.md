# unity03ex2. Sprite、Sprite Renderer
________________________________________
## 1. Spriteの基本設定
________________________________________
Sprite エディター - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/SpriteEditor.html

スプライトアトラス V2 - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/SpriteAtlasV2.html
________________________________________
### 1.1. 必要なPackageの追加や設定

1. Windows > Package Manager > Unity Registry > 2D Sprite
2. Edit > Project Settings > Editor > Sprite Atlas > Mode > V2 Enabled

________________________________________
### 1.2. Spriteモードと典型的な設定

通常

1. Texture Type > Sprite
2. Sprite Mode > Single
3. MeshType > Tight

9スライス

1. Texture Type > Sprite
2. Sprite Mode > Single
3. MeshType > FullRect

※ 描画の最適化はAtlasで解決できるため、特別な理由がなければMultipleは不要

________________________________________
### 1.3. Sprite Atlasにまとめる

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
※ Compression設定は個別とAtlasで2段階行われる。個別画像はNoneが推奨。また、ドット画像の場合はAtlasもNone推奨

________________________________________
## 1.3. 9スライスの用途

典型的な用途

- 円画像を用意して、丸角の枠にする
- 枠画像を用意して、メッセージウインドウの枠にする

________________________________________
## 2. Sprite Renderer
________________________________________
ビルボードを表示する最もシンプルな方法がこれ

- 略
