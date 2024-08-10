# eco-01ex3. Visual Studio 20xx
________________________________________
## 1. 設定関連
________________________________________
### 1.1. 設定リセット方法

1. ツール > 設定のインポートとエクスポート > すべての設定をリセット
2. 保存 > C# > 完了 

※ Visual Studio 2022のいつごろからか、エラーが出るようになった  
実害はほぼないので気にしない

________________________________________
###  1.2. おすすめの設定

ツール > オプション関連

- 行番号表示（テキストエディタ > C#）
- CodeLensオフ（テキストエディタ > すべての言語 > CodeLens）

ツール > カスタマイズ > コマンド関連

- 保存オプションの詳細設定（メニューバーをファイル > コマンドの追加 > ファイル > 保存オプションの詳細設定）
________________________________________
### 1.3. プロジェクト毎で設定したい場合

editorconfigの追加

1. ファイル追加 > editorconfigファイル(.NET)
2. editorconfigファイルを開き、一通りスクロール

推奨調整

- コードスタイル > this.とMe.の設定 > 優先する/提案事項
- コードスタイル > プライマリコンストラクターを優先する > いいえ

________________________________________
## 2. 頻出ショートカット
________________________________________
※ C#として設定をリセットすると、ctrl+k系がctrl+e系でも処理されるようになる

ctrl+kはおそらくkey shortcutの意味だが、eって何由来…？

操作                    |入力|由来
------------------------|------------------|----
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