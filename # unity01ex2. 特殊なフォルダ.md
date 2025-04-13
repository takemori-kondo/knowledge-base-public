# unity01ex2. 特殊なフォルダ
________________________________________
特殊なフォルダー名 - Unity マニュアル  
https://docs.unity3d.com/2022.3/Documentation/Manual/SpecialFolders.html

特殊フォルダーとスクリプトのコンパイル順 - Unity マニュアル  
https://docs.unity3d.com/2022.3/Documentation/Manual/ScriptCompileOrderFolders.html

Resources フォルダー - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/BestPracticeUnderstandingPerformanceInUnity6.html

プラグイン - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/Plugins.html
________________________________________
## 1. 特殊フォルダ
________________________________________
特殊フォルダ一覧

フォルダ                |Asset直下|役割
------------------------|------|------------------------------------------
Editor                  |ー    |Editorモードで機能するスクリプトになる
Editor Default Resources|必須  |略
Gizmos                  |必須  |略
Plugins                 |必須  |古いバージョンの名残
Resources               |ー    |常にビルド結果に含まれる。Resourcesクラス経由でLoadできる
Standard Assets         |必須  |略
StreamingAssets         |必須  |Assets直下に1つだけ配置。略

.

特殊フォルダとコンパイル順

1. Standard Assets、Plugins配下
2. Standard Assets、Plugins配下のEditor配下
3. 大半
4. Editor配下

________________________________________
## 2. Assets/Plugins
________________________________________
Assets/Pluginsの現在

- かつてはdllなどを含める場合はこの配下にする必要があった。現在はdll等の配置に制限はない
    - 旧来の作法に従うと、プラットフォームやCPU毎で含むかどうかが設定済になる効果はある
- スクリプトをこの配下に含めasmdef未定義の場合、デフォルトコンパイルが分かれる
    - Assembly-CSharp-firstpass になり、通常より先にコンパイルされる