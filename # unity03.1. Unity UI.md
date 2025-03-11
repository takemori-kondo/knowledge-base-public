# unity03.1. Unity UI
________________________________________
Unity UI - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/com.unity.ugui.html

Namespace UnityEngine.UI | Unity UI | 2.0.0  
https://docs.unity3d.com/Packages/com.unity.ugui@2.0/api/UnityEngine.UI.html

Unity Performance Tuning knowledge book  
https://github.com/CyberAgentGameEntertainment/UnityPerformanceTuningBible

Unity UI パフォーマンス最適化のヒント  
https://unity.com/ja/how-to/unity-ui-optimization-tips

Unityでパフォーマンスの良いUIを作る為のTips  
https://www.docswell.com/s/UnityJapan/ZJEGGK-unityuitips

uGUI 描画優先度のチートシート  
https://developer.aiming-inc.com/ux/ugui-layer/

DOTween (HOTween v2) | Animation Tools | Unity Asset Store  
https://assetstore.unity.com/packages/tools/animation/dotween-hotween-v2-27676

________________________________________
## 0. UIの基本的なプラクティス
________________________________________
パフォーマンス大原則

1. Canvasは分割
2. Imageで単色の矩形描画する場合、Source Imageには4x4の白地用意しアトラス化して設定する
3. Layoutコンポーネントは避ける
4. RaycastTargetは無効を基本とする
5. マスクはRectMask2dの方が軽いが、これも有効だと毎フレームカリングが発生する
6. TextMeshProでは、非stringは変換して渡すよりSetText()でフォーマット
7. UIを非表示にする場合、3種の使い分けが必要。とりあえずCanvas無効がバランスが良い
    - SetActive(false)：切替コスト100倍、描画&Update無効化
    - Canvasを無効：切替コスト20倍、描画のみ無効化
    - CanvasGroupで透明&Raycast無効：切替コスト1倍、描画&Updateは有効のまま
8. UI要素を非Activeにして別の親に退避する場合、先に非アクティブにする
9. AnimatorよりDOTweenを使え
    - Animatorはそのままだと毎フレーム常に処理されてしまう

制作効率上の原則

1. Screen space - Cameraを使うか、ScriptでOverlayを機械的に変換できるようにしろ
    - UIパーツPrefab化すること考えると、Cameraとの紐づけは後でしたくなる
        - スクリプトで実行時に変更&カメラ紐づけがおススメ
    - Sorting Layerで非UIとの描画順を制御できる
2. ParticleSystemの描画順に困ったら、Sorting Layerを増やしてRendererのプロパティを設定しろ
3. Canvas ScalerはHD、FullHD、スマホ（9:19.5～21）どれにするか始めに決めろ
4. Atlasは最初から設定しておけ
    - フォルダ単位で2048x2048Atlas化するよう整理すると直感的でGood
5. UI Prefabは「UI Prefabの正しい作り方」の通りに作れ

________________________________________
## 1. Canvasの基礎知識
________________________________________
### 1.1. Canvas

Canvasの仕組み

1. CanvasはUI描画の基本単位
    - Canvas毎に描画バッチがまとまる
    - 子孫要素の1つでも描画に変更が入るとCanvas全体のメッシュが再生成される
        - 適度に分割するのが重要
2. Canvas内は、Hierarchy順に描画されていく
3. Canvas間は、「Sort Order」 または 「Sorting Layer & Order in Layer」
    - Overlayの場合のみ「Sort Order」になる
        - Unity6000.0.24f1現在、同一の場合は後にactiveになった方が手前に来る
    - それ以外の場合は「Sorting Layer & Order in Layer」での挙動になる
        - Unity6000.0.24f1現在、同一の場合は動作不定
        - 表示とReycastの優先順位がねじれる場合もある
4. クリック判定等は、Event System、Input Moduleを介して処理される
    - CanvasをHierarchyに追加した際、まだ上記がない場合は自動で追加される

Render modeの要約

プロパティ            |説明
----------------------|-------------------------
Screen space - Overlay|常に表示したいUI。Canvas間の描画順はSort Order
Screen space - Camera |3D空間と干渉可能。Canvas間の描画順はSorting Layer & Order in Layer
World space           |3D空間に配置可能。Canvas間の描画順はSorting Layer & Order in Layer

.

Canvas Scalerコンポーネント

プロパティ                      |説明
--------------------------------|-------------------------
UI scale mode : 画面サイズに拡大|解像度に依らず見た目の大きさを一定にする
Screen Match Mode               |↑の際、画面の縦横比に対して拡大率・配置等の影響度の設定
1280x720                        |典型的な設定

.

その他のCanvas関連コンポーネント

コンポーネント  |説明
----------------|--------------
CanvasGroup     |透明度、有効無効、クリック判定を一括制御するための機能
CanvasRenderer  |Canvasに対する子孫の描画要素は全てこれも持つ
GraphicRaycaster|実質Canvasとセット。子孫要素のクリック判定を処理する

.

________________________________________
### 1.2. EventSystemとクリックRayCast

デバッグ中はEventSystemのインスペクタにクリックRayCastなどの情報が表示される

________________________________________
### 1.3. UI Prefabの正しい作り方

要点まとめ

1. Prefabルートノードは、RectTransformのみのノードにする
    - Prefabモードが、Canvas (Environment) 配下モードになる（初回時はPrefabを開きなおす必要あり）
2. Canvasは分割したいため、ルートノードの子ノードにCanvasを配置する（Canvas Scalerは不要）
3. UI Prefabをシーンに配置する際、Canvas 配下にネストする
4. Scene上のCanvasノードのみが、Canvas Scalerを持つようにする

注意点

- 上記Prefabにした場合、ButtonとDropDown追加時、中身がなぜか原点になる

詳細：ネストされたCanvasとエディタの微妙な関係

1. ネストされていないCanvasノードのRectTransform値は、保存されない
2. このCanvasがネストされた場合、値は初期値扱いになり、AnchorsやScaleが0になる

________________________________________
## 2. RectTransformの基礎知識
________________________________________
Anchors

プロパティ   |Anchors値
-------------|--------------------
top left     |Min=0,1, Max=0,1
middle center|全て0.5
stretch      |Min=0,0, Max=1,1

.

________________________________________
## 3. UGUIコンポーネントの基礎知識
________________________________________
enabled = falseの挙動

- Graphic継承クラス系は非表示になる（クリック判定は残る）
    - Image、RawImage、Mask、TMP_Text
- Selectable継承クラス系は表示はそのまま無反応になる
    - Button、ScrollBar、Slider、Toggle、TMP_Dropdown、TMP_InputField

Selectable継承クラス

- enabledの代わりにinteractableを使うと、色合い変更も行われる
- インスペクタからAuto Generate Animationで設定済みAnimator Controllerを作れる
- 既定の色合いやAnimator Controller、および調整は下記を参照の事

遷移オプション - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/script-SelectableTransition.html

自動レイアウト（便利だが処理が重い。内部ではGetComponentが呼び出される）

- Content Size Fitter
- Aspect Ratio Fitter
- Horizontal Layout Group
- Vertical Layout Group
- Grid Layout Group

自動レイアウトの要約

- 親要素側でLayout Group + ContentSizeFitter(Preferred Size)
- 末端要素側でContentSizeFitter(Preferred Size)
- さらに親がいる場合も、親要素側の設定をすれば良い
- Scroll RectのContentにアサインされるContentノードは、自動レイアウトする事になりがち

実践的なケーススタディ

複数の解像度のための UI 設計 - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/HOWTO-UIMultiResolution.html

UI 要素をコンテンツサイズに合わせる - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/HOWTO-UIFitContentSize.html

スクリプトから UI 要素を作成する - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/HOWTO-UICreateFromScripting.html

________________________________________
## 4. Sprite操作
________________________________________
Sprite エディター - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/SpriteEditor.html

スプライトアトラス V2 - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/SpriteAtlasV2.html
________________________________________
### 4.1. 必要なPackageの追加や設定

1. Windows > Package Manager > Unity Registry > 2D Sprite
2. Edit > Project Settings > Editor > Sprite Atlas > Mode > V2 Enabled

________________________________________
### 4.2. Spriteモードと典型的な設定

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
### 4.3. Sprite Atlasにまとめる

Create > 2D > Sprite Atlas

- Type : Master
- Scriptable Packer : Off
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
- Defalt
    - Compression : None （特にドット画像をまとめている場合）
- Objects for Packing
    - 含めたい内容を追加していく （フォルダごとD&Dなども可能）

※ Atlasを設定してもしばらく反映されない場合がある。Atlasプレビューや再ビルドなど試すべし  
※ Compression設定はAtlasの設定が優先される。ドット画像の場合はNone推奨