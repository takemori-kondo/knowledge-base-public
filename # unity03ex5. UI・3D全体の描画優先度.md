# unity03ex5. UI・3D全体の描画優先度
________________________________________
endering order in the Built-in Render Pipeline  
https://docs.unity3d.com/2022.3/Documentation/Manual/built-in-rendering-order.html

Unity＋スマホで3Dゲーム開発最適化するための考え方  
https://speakerdeck.com/cygames/unity-sumahode3dgemukai-fa-zui-shi-hua-surutamefalsekao-efang?slide=54

Controlling Unity's rendering order - why and how | Toca Boca Tech Blog  
https://medium.com/toca-boca-tech-blog/controlling-unitys-rendering-order-why-and-how-3c2bb1da8e78
________________________________________
### 1. 描画の優先度

要点

1. Unityの描画順序自体はRenderQueueで決まる。ただしShader自作等しない限り、実質的に以下の4つしか登場しない
    - 不透明(2000)
    - SkyBox(実質2500.5。特殊ルール)
    - 半透明(3000)
    - Overlay(4000)
2. 描画順序とは別に前後関係に対して、オクルージョンカリング、Zテストがある
3. RenderQueueが2501～3999の場合、同一のRenderQueue内同士はSortingLayerで管理できる
    - SurfaceType=OpaqueなMaterialの内容はRenderQueueが2000になるため、SortingLayerで管理できない
4. Overlayは既存のZバッファは考慮せずスクリーン座標系に直接描画する
5. 複数カメラがある場合、カメラごとに完結した描画が行われる
    - 後から描画するカメラをOverlayとしてStackするかClearFlag=DepthOnlyとすれば、先に描画された内容の上に上書き描画可能

RenderQueueを細かく制御しない場合の、実質的な優先度のまとめ

1. CameraのPriority (Camera.depth)
2. Screen Space - Overlay & Sort Order
3. SurfaceType=OpaqueなMaterialの内容の、距離
4. それ以外の、SortingLayer (sortingLayerName) & Order in Layer (sortingOrder)
    - Screen Space - CameraなCanvas
    - SpriteRenderer
    - ParticleSystemRenderer
    - SurfaceType=TransparentなMaterialのMeshRenderer など
5. 4が同一な場合、距離
6. 5が同一な場合、ヒエラルキー上の順番(後勝ち・子勝ち)