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