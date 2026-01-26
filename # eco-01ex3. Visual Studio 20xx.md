# eco-01ex3. Visual Studio 20xx
________________________________________
## 1. 設定関連
________________________________________
### 1.1. 設定リセット方法

1. ツール > 設定のインポートとエクスポート > すべての設定をリセット
2. 保存 > C# > 完了 

※ Visual Studio 2022の一部のバージョンは、エラーが出てた。最新は解消済み

________________________________________
###  1.2. おすすめの設定

- ソリューションまたはプロジェクト単位で.editorconfigは用意していく（ソリューションが無難）
- .editorconfig を VSCodeで開くようにする

推奨の.editorconfig設定

```ini
root = true

[*.cs]
#### Core EditorConfig Options ####
# https://learn.microsoft.com/ja-jp/visualstudio/ide/create-portable-custom-editor-options?view=vs-2022#supported-settings
indent_style = space
indent_size = 4
tab_width = 4
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

#### .NET Coding Conventions ####
# https://learn.microsoft.com/ja-jp/dotnet/fundamentals/code-analysis/code-style-rule-options

# this. and Me. preferences
dotnet_style_qualification_for_event = true:suggestion
dotnet_style_qualification_for_field = true:suggestion
dotnet_style_qualification_for_method = true:suggestion
dotnet_style_qualification_for_property = true:suggestion

# primary constructor
csharp_style_prefer_primary_constructors = false

# Space preferences
csharp_space_around_declaration_statements = ignore
```

※ 例えば以下のようなのはアリかもしれない

```ini
[ValueObjects/**.cs]
# primary constructor
csharp_style_prefer_primary_constructors = true:suggestion
```

※ 改行の補足

- 改行コードをLFで統一したい場合VS2022時点では、end_of_line = lfが必須
    - これがないと、コピペがファイルの改行コードを尊重せずにCRLFに変換してから張り付けてしまう

________________________________________
###  1.3. ツール > オプション > テキストエディター > コードのクリーンアップ 関連

※ この設定は、VS2022では設定リセットを行っても、リセットされない

- テキストエディター > コードのクリーンアップ > プロファイルの保存でコードのクリーンアップを選択 : プロファイル2
- テキストエディター > コードのクリーンアップ > 保存時にコードクリーンアップ プロファイルを実行する : チェック
- テキストエディター > コードのクリーンアップ > コードクリーンアップの構成 > プロファイル2
    - 不要なインポートまたは using の削除
    - ファイル ヘッダーの基本設定を適用する
    - Import または using の並び替え
    - ドキュメントのフォーマット
    - 名前空間に一致するフォルダーの基本設定を適用する

________________________________________
### 1.4. その他必要に応じて

- 環境 > ドキュメント > 特定のエンコードでファイルを保存 UTF-8 65001 
- テキストエディター > CodeLens > オフ
- テキストエディター > C# > 行番号 チェック
- テキストエディター > C# > コードスタイル > this.とMe.の設定 > 優先する/提案事項

ツール > カスタマイズ > コマンド関連

- 保存オプションの詳細設定（メニューバーをファイル > コマンドの追加 > ファイル > 保存オプションの詳細設定）

________________________________________
## 2. 頻出ショートカット
________________________________________
※ C#として設定をリセットすると、ctrl+k系がctrl+e系でも処理されるようになる

ctrl+kはおそらくkey shortcutの意味だが、eって何由来…？

操作                    |入力|由来
------------------------|----------------------
ソリューション類        |ctrl+shift+[nos]|テキストエディタ基本。new, open, save
ファイル類              |ctrl+[nos]|テキストエディタ基本。new, open, save
編集類                  |ctrl+[zxcvyf]|テキストエディタ基本。左下配置のキー
選択類                  |shift+操作|テキストエディタ基本
矩形選択                |shift+alt+操作|テキストエディタ基本
全体選択                |ctrl+a|テキストエディタ基本。all
全体検索                |ctrl+shift+f|テキストエディタ基本。find
次/前を検索             |(shift+)F3|F1ヘルプ、F2名前変更、F3検索の慣習より
整形                    |ctrl+e,[df]|document, format
コメント化/解除         |ctrl+e,[cu]|comment, uncomment
小文字化/大文字化       |ctrl+(shift+)u|upper
表示を折りたたむ        |ctrl+m,o|???
入力候補                |ctrl+space|???
ドキュメントコメント表示|ctrl+shift+space|???
リファクタリング候補    |ctrl+.|???
名前の変更              |ctrl+r,r|refactoring rename
ジャンプを戻る/進む     |ctrl+(shift+)-|???
定義にジャンプ          |ctrl+クリック or F12|???

※ 移動を戻る、移動を進むはvscodeに合わせてAlt+←→に変えたほうがいいかもしれない

スニペットワード（ワード入力後Tab2回で定型文が入る）

スニペットワード|内容
----------------|------------------------
予約語全般      |キーワードに応じた定型文
ctor            |コンストラクタ

.

________________________________________
## 3. 全ての参照とマルチターゲットビルド
________________________________________
https://learn.microsoft.com/ja-jp/nuget/create-packages/multiple-target-frameworks-project-file

- IntPtrなどを含むシグネチャはtarget framework(TFW)が異なるプロジェクト間で参照が見つからない
- 解消するには、該当の.csprjをテキストで開き、マルチターゲットに書き換えればよい