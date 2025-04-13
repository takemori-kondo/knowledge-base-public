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