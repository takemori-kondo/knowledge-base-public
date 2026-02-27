# unity03ex4. Unity UI
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

URP を使った Unity でのカメラスタッキング (チュートリアル)  
https://www.youtube.com/watch?v=OmCjPctKkjw

________________________________________
## 1. UIの基本的なプラクティス
________________________________________
前提

- 描画位置や描画内容が頻繁に変わるものは、そもそもUIではなくSprite Rendererで良い
- UIは、描画位置や描画内容が頻繁に変わらない静的なものに対して最適化されている
- Camera Stackを使えば、簡単に距離を無視した追加描画が可能。UI描画の定番
- 自分でメニュー等のパッド・キーボードでの移動操作を制御したい場合は、Navigationをオフにする
    - InputSystemの場合、Event System の Input Module の Move を None

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
10. Atlasは最初から設定しておけ
    - 2D Spriteは初期状態では含まれていないのでPackage Managerから追加が必要
    - フォルダ単位で2048x2048Atlas化するよう整理すると直感的でGood
        - unity03ex2. Sprite、Sprite Renderer を参照の事

制作効率上の原則

1. 最初にCanvasをどう制御するか方針を固めろ。URPで無難なのは以下
    1. Main Cameraは、UI Layerを除外。UI CameraをStackする
    2. UI Cameraは、UI Layerを撮影。Post Processはしない。OverlayにしてStack
    3. Canvas ScreenSpace - Camera。UI Layer
    4. UI、SpriteRenderer、ParticleSystem、はSorting Layerで制御
        - ただし、package化する場合、Layer・Tag・Sorting Layerの引き継ぎの自作が必須で微妙
2. Canvas ScalerはHD、FullHD、スマホ（9:19.5～21）どれにするか始めに決めろ
3. UI Prefabは「UI Prefabの正しい作り方」の通りに作った方が無難

Screen space - Camera と Linear色空間の嫌な関係

- Overlay以外のCanvasでは、Linear色空間の影響をもろに受ける
- アルファブレンドが値に対して直感的ではない状況になる

Screen space - Camera と ポストプロセスの嫌な関係

- Tonemappingによる余計な補正が入る
    - 真っ白な表示が208程度のグレーまで暗くなる
    - UI Cameraを専用に用意してPost Processをオフにすれば回避可能

________________________________________
## 2. Canvasの基礎知識
________________________________________
### 2.1. Canvas

Canvasの仕組み

1. CanvasはUI描画の基本単位
    - Canvas毎に描画バッチがまとまる
    - 子孫要素の1つでも描画に変更が入るとCanvas全体のメッシュが再生成される
        - 適度に分割するのが重要
        - 厳密にはGraphicリビルド、バッチリビルドの2ステップがある
2. Canvas内は、Hierarchy順に描画されていく
3. Canvas間は、「Sort Order」 または 「Sorting Layer & Order in Layer」
    - Overlayの場合のみ「Sort Order」になる
        - Unity6000.0.24f1現在、同一の場合は後にactiveになった方が手前に来る
    - それ以外の場合は「Sorting Layer & Order in Layer」での挙動になる
        - Unity6000.0.24f1現在、同一の場合は動作不定
        - 表示とReycastの優先順位がねじれる場合もある
        - 不透明な3D以外は統一的にこれで制御できる
4. クリック判定等は、Event System、Input Moduleを介して処理される
    - CanvasをHierarchyに追加した際、まだ上記がない場合は自動で追加される
    - Canvas側はGraphicRaycasterがクリック判定を制御する。これがないと反応しない

Render modeの要約

プロパティ            |説明
----------------------|-------------------------
Screen space - Overlay|常に最前面のUI。使いづらい(※)。Canvas間の描画順はSort Order
Screen space - Camera |3D空間と干渉可能。Canvas間の描画順はSorting Layer & Order in Layer
World space           |3D空間に配置可能。Canvas間の描画順はSorting Layer & Order in Layer

(※) 演出をUIの手前だすなら、素直に初めからCameraにした方が良い

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
CanvasRenderer  |Canvasに対する子孫のUI要素は全てこれも持つ
GraphicRaycaster|実質Canvasとセットで必須。これが子孫要素のクリック判定を処理する

.

________________________________________
### 2.2. クリック判定

- EventSystem (Input Module) とGraphicRaycasterがこの処理を担っている
    - デバッグ中はEventSystemのインスペクタにクリックRayCastの情報が表示される
- 各UI要素はRaycastTargetプロパティがある

________________________________________
### 2.3. UI Prefabの正しい作り方と使い方

要点まとめ

1. Prefabルートノードは、RectTransformと制御用の自作スクリプトのみにする
    - この状態かつ子孫にUI要素があると、PrefabモードがCanvas (Environment) 配下モードになる
        - ※ 初回時はPrefabを開きなおす必要あり
        - 逆に、Environment配下モードから元に戻したい場合、RectTransformを外したり、CanvasのRectTransform周辺の値を全て0にする必要がある
2. Canvasを分割したい場合、ルートノードの子ノードにCanvasを配置する（Canvas Scalerは不要）
3. UI Prefabをシーンに配置する際、Canvas 配下にネストする
4. Scene上のCanvasノードのみが、Canvas Scalerを持つようにする

注意点1：中身の不具合

- 上記Prefabにした場合、ButtonとDropDown追加時、中身がなぜか原点になる
   - 中身のRectTransformは手動で元に戻す必要があり

注意点2：ネストされたCanvasとエディタの微妙な関係

1. ネストされていないCanvasノードのRectTransform値は、保存されない
2. Canvasがネスト・継承配下に最初になる際、インスペクタのRectTransformが0で初期化される
    - 子CanvasはPos=(0,0,0), WH=(100,100), Anchors=中心, Pivot=(0.5,0.5), Scale=(1,1,1)を手動で再設定が推奨

________________________________________
## 3. RectTransformの基礎知識
________________________________________
Anchors

プロパティ   |Anchors値
-------------|--------------------
top left     |Min=0,1, Max=0,1
middle center|全て0.5
stretch      |Min=0,0, Max=1,1

.

________________________________________
## 4. 各種uGUIコンポーネントの基礎知識
________________________________________
### 4.1. Graphic継承クラス、Selectable継承クラス

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

________________________________________
### 4.2. Toggle

- スクリプトでの制御はisOn
- Graphicの表示制御はFadeプロパティに任せ、勝手に触らない
    - そこを制御したくなったら、完全自作したほうが早い

________________________________________
### 4.3. 自動レイアウト

自動レイアウト（便利だが処理が重い。内部ではGetComponentが呼び出される）

- Content Size Fitter
- Aspect Ratio Fitter
- Horizontal Layout Group
- Vertical Layout Group
- Grid Layout Group

自動レイアウトの要約

- 方針1
    - 親要素側でLayout Group + ContentSizeFitter(Preferred Size)
    - 末端要素側でContentSizeFitter(Preferred Size)
- 方針2
    - 親要素側でLayout Group(ControlChildSize) + ContentSizeFitter(Preferred Size)
    - 末端要素側で自作スクリプト+LayoutElement
- さらに親がいる場合も、親要素側の設定をすれば良い
- Scroll RectのContentにアサインされるContentノードは、自動レイアウトする事になりがち
- Prefabで自動レイアウトを使うと、Hierarchyに配置した際に、常に編集した扱いになってしまう
    - 見た目が鬱陶しいが、仕方なし

TMP + NotoSansJP-Regular でpx換算値が欲しい時

px|HD pt |FHD pt
--|------|------
30|20.718|31.077(実測)
40|27.624|41.436(予想)
50|34.530|51.795(予想)

.

LayoutGroup 各項目の内容

項目              |意味
------------------|-----------------
Padding           |この要素と子要素間のPadding
Spacing           |子要素同士間の隙間
Child Alignment   |子要素全体の配置基準
Reverse Arangement|子要素の配置順を逆順にするか
Control Child Size|子要素のRectTransformをその要素のLayoutElementで上書きするかどうか
Use Child Scale   |未調査
Child Force Expand|均等配置・拡張制御、など。いわゆるCssのflexのような機能

.

実践的なケーススタディ

複数の解像度のための UI 設計 - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/HOWTO-UIMultiResolution.html

UI 要素をコンテンツサイズに合わせる - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/HOWTO-UIFitContentSize.html

スクリプトから UI 要素を作成する - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/HOWTO-UICreateFromScripting.html

