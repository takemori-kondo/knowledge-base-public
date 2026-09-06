# unity03ex2. Particle System
________________________________________
## 1. Particle System
________________________________________
### 1.1. よく作るパーティクル

PPU100Spriteに対してPixel等倍距離に配置する前提の場合

値・モジュール          |dust系                   |shockwave系
------------------------|-------------------------|-----------------
Duration                |0.2                      |0.4
Looping                 |off                      |off
Start Lifetime          |0.2~0.6                  |0.4
Start Speed             |1~3                      |0
Start Size              |表示pixelの1/100         |表示pixelの1/100
Start Color             |ランダム2色間            |白
Emission                |1Burst(10~20 count)      |1~4 Burst(1行1つでディレイかけるも良し)
Shape                   |半径を0.01               |半径を0.01
Color over Lifetime     |33%まで100%              |好みで
Size over Lifetime      |使わない                 |Start Sizeに対する倍率
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
### 1.2. World空間重力に準じた土煙や爆発エフェクト

- Simulation Space を Worldに
- 「移動に応じたEmmition」は Rate over Distance で対応
- 「小 → 大 → 小」は Size over Lifetime で対応

________________________________________
### 1.3. 各種小技

#### 1.3.1. Emissionの停止

- Stop()

#### 1.3.2. スプライトアニメ

- SpriteをTexture Sheet Animationに設定
- Sprite同士は同一Atlas必須