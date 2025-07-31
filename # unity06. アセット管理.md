# unity06. アセット管理
________________________________________
アセットワークフロー  
https://docs.unity3d.com/ja/2022.3/Manual/AssetWorkflow.html

アセットバンドル  
https://docs.unity3d.com/ja/2022.3/Manual/AssetBundlesIntro.html

はじめに | Addressables | 2.3.1  
https://docs.unity3d.com/Packages/com.unity.addressables@2.3/manual/AddressableAssetsGettingStarted.html

Unity Addressable Asset System を使いこなす  
https://zenn.dev/allways/articles/f172da98410e2f
________________________________________
## 1. 概要
________________________________________
アセットに関わるフォルダ

- Resources (Addressablesが出来たため非推奨)
- StreamingAssets
- AddressableAssetsData 

主な方針

- Resources
    - 未使用リソースも常にビルドに含まれる
    - ビルドは分割できない
- AssetBundles
- AssetBundles - Streaming Utage Resource Converter
- AssetBundles - Addressable Assets System
    - AssetBundleの分割や重複の管理は、チーム開発においてレギュレーションが必要

________________________________________
## 2. AddressableでAssetBundles
________________________________________
### 2.1. Addressableの特徴

Addressableは、AssetBundlesで自前管理が必要だった機能を実装済みにしたヘルパライブラリ

1. Resourcesに近い使用感覚でAssetBundleを使える
    - アドレスは実際のフォルダ階層とは無関係で、自由に設定する
    - グループはAssetBundleと対応させるか、1つ1つ個別にすることが選べる
    - Addressable使用側は、それがローカル/リモート等どこであるかも意識する必要がない
2. AssetReference型によりエディタや[SerializeField]と統合
    - AssetReference型は明示的なロードが必要で、それまでロードされない
3. 参照カウント管理を内蔵
4. AssetBundle間の依存関係を自動解決
    - 自前でmanifestロードを書く必要がない

AssetBundlesの注意点

- UnityWebRequestAssetBundleやAddressableでリモートからDLした場合、キャッシュされる
- キャッシュは永続化されているわけではないため、150日以上保存されない
- 永続化したい場合は、AssetBundles内の機能でDLせず普通にダウンロードしてローカルに永続化してから読むとよい

________________________________________
### 2.2. Addressableの使い方

セットアップ

1. Window > Package Manager > Unity Registry > Addressablesで検索してinstall
2. Window > Asset Management > Addressables > Groups > Create Addressable Settings
    - AddressableAssetsDataフォルダが追加され、各種設定ファイルが初期状態で用意される
3. 再起動（Projectペインの右クリックメニューに必要）
4. AddressableにしたいフォルダかAssetを選択して、InspectorからAddressableにチェック
5. （オプション）アドレスやグループを個別に構成・調整する

使用

- AddressableにしたAssetは、AssetReferenceで割り当て可能。assetReference.LoadAssetAsync()で遅延ロード
    - AssetReference.Release()で解放
- Resourcesの代わりに、Addressables.LoadAssetAsync()でロード
    - Addressables.Release()で解放
- ロードされたprefabなどは、Addressables.InstantiateAsync()でインスタンス化
    - Addressables.ReleaseInstance()で解放
- 別プロジェクトで作成したAddressableカタログをロード
    - Addressables.LoadContentCatalogAsync()

________________________________________
### 2.3. 実践的な分割とロード

Manage catalogs at runtime | Addressables | 2.2.2  
https://docs.unity3d.com/Packages/com.unity.addressables@2.2/manual/LoadContentCatalogAsync.html

ホロライブアプリでのAddressable AssetSystem活用事例｜カバー株式会社　公式note  
https://note.cover-corp.com/n/nba3cbd32f74f

- Build Remote CatalogをONにしてカタログを生成

GitHub - CyberAgentGameEntertainment/SmartAddresser  
https://github.com/CyberAgentGameEntertainment/SmartAddresser

________________________________________
### 2.4. Addressableの典型的なプラクティス

1. Pack Separatelyがいい（1アセット1Addressable Asset）
2. Addressables.LoadAssetAsync()だけ使い、handleを使って解放せよ
3. InstantiateはAddressable版は使わず、Object版で良い
    - あるインスタンスがAddressable由来なのかそうでないのかは、インスタンスからはぱっと見分からない
    - 分からないのであれば、どちらかを使用禁止にした方がまし
    - 通常のInstantiateや子Prefabを禁止するのは現実的ではないから、Addressable版を禁止する
4. DisposableHandleヘルパを用意して、using管理するとよい