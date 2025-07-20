# unity08ex. Asset Bundlesの詳細
________________________________________
## 1. AssetBundles
________________________________________
### 1.1. AssetBundles の概要

- 各Assetをプラットフォーム圧縮
- 形式は独自アーカイブ化 + LZMAかLZ4再圧縮
- LZ4キャッシュ管理 + API

※ Resourcesフォルダと干渉するため、Resources下のAssetに対して操作は非推奨

________________________________________
### 1.2. 旧来の方法（Addressableを使えばこの方法は不要）

アセットバンドルのワークフロー  
https://docs.unity3d.com/ja/2022.3/Manual/AssetBundles-Workflow.html

準備とAssetBundle化

1. フォルダまたはAssetを選択し、インスペクタ下部でAssetBundleを指定
    - AssetBundleはフォルダ構造が可能
    - Variant名（AssetBundleの別名）は未指定でもOK
2. お試しスクリプトでWindows向けにビルド（=圧縮&アーカイブ）する
    - ↑のサイトを参照の事

AssetBundleの使用（同伴アーカイブの場合）

```cs
using UnityEngine;

        // AssetBundle自体のロード
        // Application.streamingAssetsPathは、プラットフォームごとのデフォルトフォルダを参照する
        // Win : Application.dataPath + "/StreamingAssets"
        // iOS : Application.dataPath + "/Raw"
        // And : "jar:file://" + Application.dataPath + "!/assets/"
        var loadedBundle = AssetBundle.LoadFromFile(Path.Combine(Application.streamingAssetsPath, "myassetBundle"));

        // Assetのロード
        var prefab = loadedBundle.LoadAsset<GameObject>("MyObject");
```

AssetBundleの使用（非同伴アーカイブの場合）

```cs
using UnityEngine;
using UnityEngine.Networking;

// ...

        // AssetBundle自体のロード
        // この方法でダウンロードされたAssetBundleは圧縮形式に関わらず、ローカル側では解凍後にlz4で圧縮されてキャッシュされる
        string url = "file:///" + Application.dataPath + "/AssetBundles/" + assetBundleName;
        var request = UnityWebRequestAssetBundle.GetAssetBundle(url, 0);
        yield return request.Send();
        var loadedBundle = DownloadHandlerAssetBundle.GetContent(request);

        // Assetのロード
        var prefab = loadedBundle.LoadAsset<GameObject>("MyObject");
```

________________________________________
### 1.3. AssetBundleの動作詳細

BuildAssetBundleOptions

- None : LZMA圧縮
- UncompressedAssetBundle : 非圧縮
- ChunkBasedCompression : LZ4圧縮

BuildTarget

- 略

AssetBundle化の仕様

- AssetBundleに含めるAssetが他のAssetに依存している時、依存しているAssetも含める
- 異なるAssetBundle同士が同一のAssetに依存している時、それぞれにAssetが含まれる（重複してしまう）
- 依存Asset自体が明示的にいずれかのBundleに含まれれば、このようなことは起きない
    - ただし、依存Bundleのロードは自動的に行われないため、Load順序に気を付ける必要がある
- 依存関係自体は、manifestをロードすれば確認することが可能
    - 言い換えると、（Addressableを使わない限り）Bundle間の依存関係の解決は自前で管理が必要

```cs
var ab = AssetBundle.LoadFromFile(manifestFilePath);
ab.LoadAsset<AssetBundleManifest>("AssetBundleManifest");
string[] dependencies = manifest.GetAllDependencies("fooBundle");
```

AssetBundleのロード方法の種類

- AssetBundle.LoadFromMemoryAsync
- AssetBundle.LoadFromFile
- AssetBundle.LoadFromStream
- UnityWebRequestAssetBundle.GetAssetBundle