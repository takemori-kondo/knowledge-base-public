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

そもそもの動機

- 起動時のロードに含めず、起動時間を短縮して後でロードしたい
- 必要に応じてアンロードして、メモリを節約したい
- メインのビルドと分離して、ビルド時間を節約したい

内容                          |Resources|StreamingAssets|AssetBundles/Addressable
------------------------------|---------|---------------|------------
実行中に追加ロード            |○       |○             |○
一括シーケンシャルロード      |×※1    |×             |○
UnityEngine.Objectとしてロード|○       |×             |○
ロード結果の自動キャッシュ    |○       |×             |△
実行中にアンロード            |○       |※2            |○
ビルド時間の増加              |有り     |コピーのみ     |別ビルド
起動時のインデクス構築時間    |有り     |なし           |なし
依存関係の自動解決            |○       |×             |△
パッチ/DLCとして差し替え      |×       |×※3          |○

※1 見た目はビルドに内包されるし1つのファイルのようになるが、各アセットは個別にファイルロードすることになる。  
LoadAllを使えばフォルダ相当単位の一括ロードはできるが、結局個別ファイルごとのアクセスになる。  
AssetBundleのようにBundle単位でまとめてメモリにロードすることが出来ない

※2 Streaming Assetsは、プラットフォームのI/Oで直接ファイルを触る方法を提供するシンプルな仕組み

※3 PCでかつファイル保存の制約がなければ可能だが、通常はプラットフォームが提供する専用の領域に保存が必要になるため不可能 


主な違い

- Resources
    - 未使用リソースも常にビルドに含まれる
    - ビルドは分割できない
    - 中規模以上で分業が必要になったら、非推奨
- StreamingAssets
    - 実行ファイルに含めず、生データをそのまま付属させるための機能
    - 実務的には、主に開発分業のために使われることが多い
        - いわゆるスクリプトエンジンプログラミングでのtext外出しに対応させる
        - ビルド後にテキストアセットのデバッグ・調整を行う分業の必要性が出てきたら、必須
    - 環境によってパスが異なるため、Application.streamingAssetsPath で吸収
        - Android, WebGL環境は上記とも異なるアプローチになる（URIでアクセスする）
- AssetBundles
- AssetBundles - Streaming Utage Resource Converter
- AssetBundles - Addressable Assets System
    - AssetBundleの分割や重複の管理は、チーム開発においてレギュレーションが必要

実務上の注意点

1. スマホやコンシューマ実機は、CPUが遅い
    - 大量のCsv, Json, XMLを実行時に解釈してしまうと非常に遅い。ロード時間の原因になりやすい
        - そのため、最終ビルド版ではStreaming Assetsのテキストを読まず、ScriptableObjectなどにしておく

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