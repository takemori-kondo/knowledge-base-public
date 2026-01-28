# unity01ex3. ライブラリ運用
________________________________________
## 1. 定番アセットやライブラリ
________________________________________
### 1.1. 追加可能な公式ツール

- 2D Animation (Anima2Dの後継)
- 2D Sprite
- 2D SpriteShape
- 2D Tilemap Editor
- 2D Tilemap Extras
- Addressables
- Addressables for Android
- Cinemachine
- Polybrush
- Post Processing
- TextMeshPro
- Visual Studio Tools for Unity (Visual Studio Editor)
________________________________________
### 1.2. 定番サードパーティ

DOTween Free  
https://assetstore.unity.com/packages/tools/animation/dotween-hotween-v2-27676

UniTask  
https://github.com/Cysharp/UniTask

________________________________________
### 1.3. 定番ライブラリ

Nuget

- CsvHelper（一部機能は実機で動かない）
- Dapper
- Newtonsoft.Json（遅い。Unity付属Utility推奨）
- NCalc（MathHelperを回避するコードの追加が必要）

その他

- 宴
- Photon

________________________________________
## 2. ライブラリの追加方法
________________________________________
### 2.1. .unitypackage、UPM

別ページを参照の事

- unity08. Unity package、template

________________________________________
### 2.2. nuget管理下

- NuGet for Unityを組み込んで利用が圧倒的に楽

https://github.com/GlitchEnzo/NuGetForUnity.git?path=/src/NuGetForUnity

________________________________________
### 2.3. ネイティブライブラリ

対応するフォルダに配置

- Assets/Plugins/iOS
- Assets/Plugins/x86_64

※ Android、Switch、PS4なども必要に応じて追加・Inspectorで調整が可能