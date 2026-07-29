# unity02ex4. メモリリーク
________________________________________
## 1. メモリリークのチェック
________________________________________
### 1.1. 基本的なこと

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
### 1.2. obj = null;

要約：リソースのUnload系を使うなら、obj = null; も大事

1. UnityEngine.Object継承クラスはDestroyされると!=、==演算子的にはnull扱いになる
2. しかし実際にはC#としての参照は残っている
3. リソースのUnload系は、C#の参照を基準としてアンロード判定する
    - Resources.UnloadUnusedAssets() など
        - なお不思議だが、ラムダ式でキャプチャされただけの場合、回収対象らしい