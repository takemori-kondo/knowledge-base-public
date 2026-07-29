# unityPractices01. Directory & Scene
________________________________________
Unity のベストプラクティス  
https://unity.com/ja/how-to

Unity プロジェクトを整理するためのベストプラクティス  
https://unity.com/ja/how-to/organizing-your-project

________________________________________
## 1. フォルダ構成
________________________________________
典型的な構成：ファイルタイプベース

- Assets
    - AddressableAssetsData
    - App
        - Art
            - Materials
            - Models
            - Prefabs
            - Shaders
            - Textures
        - Audio
            - Music
            - Sound
        - Code
            - Runtime
            - Tests
        - Docs
        - Scenes
        - UI
            - Animations
            - Fonts
            - Materials
            - Prefabs
            - Shaders
            - Textures
    - Plugins
    - Settings
    - StreamingAssets

※ AddressableAssetsDataは移動すると変になるので、この位置

典型的な構成：機能ベースの例

- Assets
    - AddressableAssetsData
    - App
        - _Common
            - Animations
            - Audio
            - Effects
            - Fonts
            - Materials
            - Models
            - Prefabs
            - Shaders
            - Textures
        - _Docs
        - _Runtime
        - _Scenes
        - _Tests
        - Feature1
            - XXX.prefab
            - XXX.controller
            - XXX_hidden.anim
            - XXX_hiding.anim
            - XXX_showing.anim
            - XXX_shown.anim
            - XXX.mat
            - XXX_r.mat
            - XXXView.cs
            - YYY.prefab
            - ...
        - Feature2
        - Feature3
    - Plugins
    - Settings
    - StreamingAssets

________________________________________
## 2. シーン構成
________________________________________
典型的な構成 (単シーンの場合)

- AudioRoot
- GraphicsRoot
    - Main Camera
    - Directional Light
    - Global Volume
- UIRoot
- TapEffect
- FadeEffect
- EventSystem

3D・UI集約Prefab（ほぼシーンのようなPrefab）を利用する場合は、UIRootなどは使わない形になりがち

典型的な構成 (マルチシーンの場合)

- ○○MainScene
- ○○UIScene
- ○○EffectScene
- ○○LightScene

2018.2以前はプレハブのネストが出来なかったこともあり、マルチシーンは常識化していた  
2025年現在もアニメーションの制限やDOTSの兼ね合いから、有力

商業レベルのグラフィックを利用する場合

- グラフィクスの事前計算系の機能のいくつか、Prefabでは使えずシーンにのみ使える
- ただし、それでも3D・UI集約Prefabが無駄になるわけではないため、最初は集約Prefabを用意していくと良い