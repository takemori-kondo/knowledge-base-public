# unity03.6. Test Code
________________________________________
## 1. EditModeテストの初期設定

1. 予め、スクリプトをasmdef管理下にしておく
    - asmdef経由で参照する必要があるため
2. Testsフォルダ & asmdef の作成
    - 作りたい場所の親フォルダ右クリック > Create > Testing > Test Assembly Folder
3. Tests.asmdef 調整
    - Assembly Definition References > テスト対象のasmdefを追加
    - Platforms > Editor のみにする
        - Any Platform のチェックを外し、Deselect Allし、Editorをチェック
4. EditorフォルダをTests直下に追加し、その下に自由にTestコードを定義していく

________________________________________
## 2. 注意点


- ignoreしたい場合、Ignore属性ではなく以下を呼ぶ
    - Editorモードテストの場合 : Assert.Ignore()
    - Playモードテストの場合 : Assert.Inconclusive()

________________________________________
## 3. NSubstituteの追加方法

ここで紹介する方法の前提事項

- Project Settings > Player > Other Settings > Configuration
    - IL2CPP
    - .NET Standard2.1
    - Default (.NET Framework)
- .NET Frameworkでしか簡単にセットアップできないので、Editorでのみ使用する

追加方法

1. 以下からDownload packageでDL、解凍、配置
    - https://www.nuget.org/packages/NSubstitute/
    - https://www.nuget.org/packages/castle.core/
    - .nupkgを.zipにリネームして解凍
    - net462版のdllを適当なTests/Editor/以下に配置
2. Unityからdllを選択し、inspectorで以下のようになっていることを確認
    - Select platforms for plugin はチェックなし
    - Include platforms は Editorのみにチェック
3. dllを使用したいasmdefのAssembly Referencesにそれぞれ追加