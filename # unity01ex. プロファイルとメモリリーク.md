# unity01ex. プロファイルとメモリリーク
________________________________________
Unity Performance Tuning knowledge book  
https://github.com/CyberAgentGameEntertainment/UnityPerformanceTuningBible

OptimizeYourGamePerformancePC_JP.pdf  
https://unity3d.jp/wp-content/uploads/2025/01/OptimizeYourGamePerformancePC_JP.pdf

Optimize-Your-Mobile-Game-Performance_JP.pdf  
https://unity3d.jp/wp-content/uploads/2025/01/Optimize-Your-Mobile-Game-Performance_JP.pdf
________________________________________
## 1. プロファイル基礎
________________________________________
### 1.1. 性能要件の決定

性能要件を決める

1. 最低動作環境を決める
2. ボリュームゾーン環境への保証性能を決める

ボリュームゾーン環境の典型的な目標

項目          |内容
--------------|-------------------------
対象機種      |5年前のミドルレンジやiPhone SE、今年の有名機種エントリーモデル
フレームレート|60fps
メモリ        |1GB
遷移時間      |3秒以内
熱            |誰でも持ち続けられる
バッテリー    |1時間で20%以内

.

________________________________________
### 1.2. プロファイルのアプローチ

1. メモリとFPSは画面に出す
2. チューニングする前に原因が確定するまで計測する
    - クラッシュ （メモリ or プログラム）
    - 処理落ち（CPU or GPU）
    - ロード時間 （ファイルサイズ）
3. 詳細な調査はUnity Profilerを使う
4. 「実機とUnity Profilerの接続」を行い、実機で確認する
    - Editor Loopなど、エディタ上ではOthersに出てくる分もなくなり、見通しが良くなる


________________________________________
### 1.3. Unity Profiler基礎 (Window > Analysis > Profiler (Ctrl + 7))

画面上部バー

- Profiler Modulesでプロファイルする項目を選択できる
    - GPU Usageを表示すると、CPU Usage・GPU Usageに大きな影響が出る
    - そのためデフォルトでOffかつ「！」表示がある
    - それ以外もCPU処理を食うため、必要なものだけ表示が推奨
- Play Mode：後述の実機接続の場合に切り替える
- ⦿がオンだと、プロファイルし続ける
- Clear On Playを有効にすると、Playするたびにリセットされる
- Deep Profileを有効にすると、？？？
- Call Stacksを有効にすると、？？？

各Usage

- 初期設定では直近300フレーム分のタイムラインが保持される
- 左側の項目で、表示非表示を切り替え可能

画面下半分

- あるフレームの詳細を表示する
- 左上のドロップダウンで各Usageごとの表示形式切り替え可能
- CPU Usage
    - TimeLine。タイムラインで表示する
    - Hierarchy。コールスタックで表示する
    - Inverted Hierarchy。コールスタックを逆表示する
    - Raw Hierarchy。？？？
- Memory Usage
    - Simple。種別ごとの総使用量だけ表示
    - DetailedはProfiler Memoryの正式リリースに伴い、これを開くためのリンクだけが残っています
        - 初回のSnap Shot時、パスワードとか機密情報も見れちゃうよ？という警告出ます

※ Simpleでは以下のメモリ使用量がわからないため注意

- プラグインが確保したメモリ
- IL2CPPメタデータ

________________________________________
### 1.4. 実機とUnity Profilerの接続

開発ビルドでビルドしたアプリは、実機とUnity Profilerが接続できる

1. 開発ビルドされたアプリを実機側にインストール
2. USB接続モードが一通り設定済でUnityに実機を認識させる
    - Profiler > PlayModeに実機名が選択可能ならOK
3. 実機でアプリを開始
4. Profiler > PlayModeを実機に切り替える（アプリが起動してから指定する必要あり）

※ USB接続や開発ビルドの詳細は unity03ex4. android などを参照のこと

________________________________________
## 2. メモリリークしやすいポイント
________________________________________
### 2.1. 基本的なこと

1. 典型的な動的追加
    - 動的にnewしたUnityEngine.Object継承クラス全般
    - Sprite.Create()、PlayableGraph.Create() など
    - Object.Instantiate()
    - GameObject.AddComponent()
    - リソースのLoad系
        - Resources.LoadAsync()
        - AssetBundle.LoadAssetAsync()
        - Addressables.LoadAssetAsync() など
    - DownloadHandler系
        - DownloadHandlerTexture.GetContent() など
        - DownloadHandlerTexture.texture など
2. Renderer.material
3. MeshFilter.mesh

________________________________________
### 2.2. obj = null; と リソースのUnload系

要約：リソースのUnload系を使うなら、obj = null; も大事

1. UnityEngine.Object継承クラスはDestroyされると!=、==演算子的にはnull扱いになる
2. しかし実際にはC#としての参照は残っている
3. リソースのUnload系は、C#の参照を基準としてアンロード判定する
    - Resources.UnloadUnusedAssets() など
        - なお不思議だが、ラムダ式でキャプチャされただけの場合、回収対象らしい