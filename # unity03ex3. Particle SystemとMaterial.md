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
### 2.1. マテリアルの基本的な概念

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
### 2.2. 基礎的なマテリアルの操作

sharedMaterialは直接変更、materialはクローンして変更

項目        |旧パイプライン(Built-in)        |URP
------------|--------------------------------|--------------------------------
アルベド    |.SetColor("_Color", val)        |.SetColor("_BaseColor", val)
テクスチャ  |.SetTexture("_MainTex", val)    |.SetTexture("_BaseMap", val)
金属さ      |.SetFloat("_Metallic", val)     |.SetFloat("_Metallic", val)
ツルツルさ  |.SetFloat("_Glossiness", val)   |.SetFloat("_Smoothness", val)
自己発光色  |.SetColor("_EmissionColor", val)|.SetColor("_EmissionColor", val)
法線マップ  |.SetTexture("_BumpMap", val)    |.SetTexture("_BumpMap", val)

.

URP物理ベースレンダリングのデフォルトで対応できるもの、できないもの

- ○ 顔料（拡散反射）
- ○ 鏡面（鏡面反射）
- ○ 凸凹の近似（法線マップ）
- ○ 環境反射の近似（キューブマップ）
- ○ 自己発光（エミッション）
- △ 半透明
- × 屈折
- × サブサーフェス。皮膚、蝋、葉っぱ
- × 構造色
- × 異方性反射。髪、シルク
- × 偏光・分光。CD、プリズム

________________________________________
## 3. シェーダ
________________________________________
### 3.1. ビルドインシェーダ

既定のシェーダーのソースコード(リンクはいつか復旧するはず…はず…)

https://unity3d.com/download_unity/builtin_shaders.zip

DefaultResourcesExtra/Mobile/Mobile-Particle-Add.shader
DefaultResourcesExtra/Mobile/Mobile-Particle-Alpha.shader

________________________________________
### 3.2. URPシェーダ

パッケージとしてプロジェクトに含まれる。例えば以下

Library\PackageCache\com.unity.render-pipelines.universal\Shaders\Particles\ParticlesUnlit.shader

________________________________________
### 3.3. シェーダの文法

シェーダー  
https://docs.unity3d.com/ja/2019.4/Manual/SL-Reference.html

Shader languages reference  
https://docs.unity3d.com/6000.0/Documentation/Manual/shaders-reference.html

前提知識：典型的な処理順序

1. 頂点シェーダ
2. カリング&深度テスト
3. フラグメントシェーダ
4. アルファテスト
5. ブレンド

概念

- Unityは大半の記述をマクロにすることでDX9スタイルとDX10+スタイルの差異を吸収している

基本的な型

型          |意味
------------|----------
fixed       |URPでは使用不可。レガシー用。11bit固定小数
half        |16bit小数。実際にはPCの場合、GPUは常にfloat計算になる
float       |32bit小数
xxxxx2      |2つセット。x、y
xxxxx3      |3つセット。x、y、z
xxxxx4      |4つセット。x、y、z、w
xxxxx4x4    |4x4セット
sampler2D   |精度自動選択のテクスチャ・サンプラ両宣言型マクロ(モバイルは_half、それ以外は_float)
sampler3D   |精度自動選択のテクスチャ・サンプラ両宣言型マクロ(モバイルは_half、それ以外は_float)
samplerCUBE |精度自動選択のテクスチャ・サンプラ両宣言型マクロ(モバイルは_half、それ以外は_float)
TEXTURE2D(x)|個別宣言マクロ。テクスチャ名
TEXTURE3D(x)|個別宣言マクロ。テクスチャ名
SAMPLER(x)  |個別宣言マクロ。samplerテクスチャ名

.

基本的な操作・関数

関数          |意味
--------------|----------
tex2D(tex, uv)|

.

宣言済みマクロ

- 定型的な構造群の宣言だったり、コールバック的な記述を実現するためのもの

マクロ                      |意味
----------------------------|---------------------------
#pragma surface surf Lambert|略
#pragma vertex vert_img     |レガシー向け
#pragma vertex Vert         |典型的なもの
#pragma fragment frag       |frag()のためのマクロ

.

基本構造

```shader
Shader "Uft.FadeEffects/Legacy/GrayscalePostEffect"
{
    Properties
    {
        _Amount ("Amount", Range(0, 1)) = 0
        _MainTex ("Texture", 2D) = "white" {}
    }

    SubShader
    {
        // ポストエフェクトのお約束
        Cull Off ZWrite Off ZTest Always

        Pass
        {
            CGPROGRAM
            #pragma vertex vert_img
            #pragma fragment frag
            #include "UnityCG.cginc"

            sampler2D _MainTex;
            float _Amount;

            fixed4 frag (v2f_img i) : SV_Target
            {
                fixed4 col = tex2D(_MainTex, i.uv);

                // 明度計算（YCbCr系）
                float gray = dot(col.rgb, float3(0.299, 0.587, 0.114));

                // カラー → グレースケールの補間
                col.rgb = lerp(col.rgb, gray.xxx, _Amount);

                return col;
            }
            ENDCG
        }
    }
}
```

Properties

- プログラムやインスペクタで制御可能な変数の宣言と初期値
- [HideInInspector] でインスペクタに表示させなくなる

SubShader

代表的な設定|概要
------------|--------------------------------------
Tags        |Urpは必須
Cull Off    |選択肢はBack/Front/Off
ZWrite Off  |選択肢はOn/Off
ZTest Always|選択肢はLess/Greater/LEqual/GEqual/Equal/NotEqual/Always
Blend       |ブレンドモード
Pass        |構造を宣言したり、frag()を記述するところ

.

SubShader Pass

- CGPROGRAM、#include "UnityCG.cginc"、ENDCG
    - レガシー用の基本的な記述。基本的なマクロや関数が利用可能になる