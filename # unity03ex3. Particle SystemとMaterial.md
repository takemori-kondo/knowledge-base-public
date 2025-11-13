# unity03ex3. Particle SystemとMaterial
________________________________________
※ Unity UI以外はMaterialでTextureを設定することになるため、Materialもここで説明

________________________________________
## 1. Particle System
________________________________________
### 1.1. よく作るパーティクル

値・モジュール     |dust系                   |shockwave系
-------------------|-------------------------|-----------------
Duration           |0.2                      |0.4
Looping            |off                      |off
Start Lifetime     |0.2~0.6                  |0.4
Start Speed        |5~15                     |0
Start Size         |表示サイズの1/10         |表示サイズの1/10
Start Color        |ランダム2色間            |白
Emission           |10~20 Burst              |1~4 Burst
Shape              |どれでも                 |半径を0.01
Color over Lifetime|33%まで100%              |好みで
Size over Lifetime |使わない                 |Start Sizeに対する倍率
Renderer - Material     |Mobile/Particles/Additive|URP/Particles/Unlit
Renderer - Sorting Layer|Effect向けのもの         |Effect向けのもの

.

シェーダーは既定の以下3つどれか使えば大体OK  
Mobile2種はレガシーシェーダだが、URPでも問題なく使える

- Mobile/Particles/Additive
- Mobile/Particles/Alpha Blended
- Universal Render Pipeline/Particles/Unlit
    - https://docs.unity3d.com/6000.0/Documentation/Manual/urp/particles-unlit-shader.html

________________________________________
### 1.2. Unity UIに従って配置する方法

要約

- スポーン位置はCanvas配下のRectTransformで決め、positionでワールド座標を取得すればよい
    - この方法なら、Canvas Scalerも反映されたサイズ・位置にスポーンできる
    - Pixel等倍の10倍の見た目にしたい場合、Canvasとの距離を予め調整しておくとよい
        - 縦Fov=60, Pixel/Unit=100, HDならば、62.353...
        - 縦Fov=60, Pixel/Unit=100, FullHDならば、93.530...

________________________________________
## 2. Material
________________________________________
### 2.1. マテリアルの基礎

DirectX9時代からある基本的なMaterialパラメータ（現代では使用されない）

- Diffuse (拡散)
- Ambient (環境光)
- Specular (反射)
- Emissive (発光)

現代の物理ベースレンダリングでは、代わりに下記になった

- Albedo
- ×廃止（ワールド側のパラメータになった）
- Smoothness & Metallic
- Emissive

________________________________________
### 2.2. ビルドインシェーダ

既定のシェーダーのソースコード(リンクはいつか復旧するはず…はず…)

https://unity3d.com/download_unity/builtin_shaders.zip

DefaultResourcesExtra/Mobile/Mobile-Particle-Add.shader
DefaultResourcesExtra/Mobile/Mobile-Particle-Alpha.shader

________________________________________
### 2.3. URPシェーダ

パッケージとしてプロジェクトに含まれる。例えば以下

Library\PackageCache\com.unity.render-pipelines.universal\Shaders\Particles\ParticlesUnlit.shader