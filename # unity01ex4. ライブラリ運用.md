# unity01ex4. ライブラリ運用
________________________________________
## 1. 定番アセットやライブラリ
________________________________________
### 1.1. 手動で追加が必要な公式ツール

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
### 1.3. 定番Nugetライブラリ

- CsvHelper（実機環境回避（GetRecordsを使わず、手動マッピング）が必要）
- Dapper
- Newtonsoft.Json（遅いため、Unity付属Utilityを使用したほうがいい）
- NCalc（実機環境問題回避（MathHelper回避）コードの追加が必要）

________________________________________
### 1.4. その他定番フレームワーク

- 宴
- Photon

________________________________________
## 2. ライブラリの追加方法
________________________________________
### 2.1. .unitypackage、UPMの場合

別ページを参照の事

- unity08. Unity package、template

________________________________________
### 2.2. nuget管理下のC#ライブラリ

- NuGet for Unityを組み込んで利用が圧倒的に楽

________________________________________
### 2.3. その他のDLL、ネイティブライブラリ

対応するフォルダに配置

- Assets/Plugins/iOS
- Assets/Plugins/x86_64

※ Android、Switch、PS4なども必要に応じて追加・Inspectorで調整が可能