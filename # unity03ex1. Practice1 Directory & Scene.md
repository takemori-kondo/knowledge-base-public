# unity03ex1. Practice1 Directory & Scene
________________________________________
https://unity.com/ja/how-to

Unity プロジェクトを整理するためのベストプラクティス  
https://unity.com/ja/how-to/organizing-your-project

________________________________________
## 1. フォルダ構成
________________________________________
典型的な構成

- Assets
    - AddressableAssetsData
    - MyProject
        - Art
            - Materials
            - Models
            - Shaders
            - Textures
        - Audio
            - Music
            - Sound
        - Code
        - Docs
        - Scenes
        - Settings
        - UI
            - Animations
            - Fonts
            - Materials
            - Shaders
            - Textures

※ SettingsはURPプロジェクトの場合のフォルダを移動してきたもの  
※ AddressableAssetsDataは移動すると変になるので、この位置

________________________________________
## 2. シーン構成
________________________________________
典型的な構成(スマホゲーム)

- GraphicsRoot
    - Main Camera
    - Directional Light
    - Global Volume
- UIRoot
- AudioRoot
- TapEffect
- FadeEffect
- EventSystem