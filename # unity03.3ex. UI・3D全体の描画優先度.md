# unity03.3ex. UI・3D全体の描画優先度
________________________________________
endering order in the Built-in Render Pipeline  
https://docs.unity3d.com/2022.3/Documentation/Manual/built-in-rendering-order.html

Unity＋スマホで3Dゲーム開発最適化するための考え方  
https://speakerdeck.com/cygames/unity-sumahode3dgemukai-fa-zui-shi-hua-surutamefalsekao-efang?slide=54

Controlling Unity's rendering order - why and how | Toca Boca Tech Blog  
https://medium.com/toca-boca-tech-blog/controlling-unitys-rendering-order-why-and-how-3c2bb1da8e78
________________________________________
### 1. 描画の優先度（描画順ではない）の要約

サイゲの54スライド目が端的

1. CameraのPriority (Camera.depth)
2. Screen Space - Overlay & Sort Order
3. SortingLayer & Order in Layer
    - SpriteRendererとParticleSystemRendererは、インスペクタから設定可能
    - その他のRendererは、スクリプトからsortingLayerNameを指定すれば可能
    - Order in Layerは、スクリプト上の名前はsortingOrder
4. RenderQueue
    - 1000 Background
    - 2000 Geometry
    - 2450 AlphaTest
    - 2500.5 SkyBox
    - 3000 Transparent
    - 4000 Overlay
    - 2500以下ならOpaqueSortMode.FrontToBack
    - 2501以上ならTransparencySortMode.Default
5. Z値
6. ヒエラルキー上の順番(後勝ち・子勝ち)

※ URPはカスタマイズ可能。なのでプロジェクトによっては上記でないかも  
※ 純粋な描画順は、カメラ毎にRenderQueue通り。ただしZ値やSortingLayerに応じて省略される  
※ インスペクタ上の名前とスクリプト上のプロパティ名が変えてあるものが多い。注意  
    sortingOrderはインスペクタ上は他の値によって呼び名変わるし…