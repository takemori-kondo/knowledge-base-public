# unityPractices03. Performance Practices
________________________________________
## 0. はじめに

別ページの以下を先に読んでください

- unity01ex. プロファイルとメモリリーク
- unity02ex2. ロギング
- unity03. Unity UI
- unityPractices02. Basic Practices

________________________________________
## 1. 対メモリ・GCパフォーマンス
________________________________________
GC対象の量産を避ける

1. むやみに文字列結合を使わない。頻繁に動的に文字列が必要なら、StringBuilderを検討する
    - String.Format、つまり$"{foo} abc"も注意。GC対象が作られる
2. むやみにLinqを使わない
3. むやみにキャプチャするラムダ式（クロージャ）を使わない
4. スタック領域に余裕があるなら、stackallocできることろは使っていく
5. 16Byte以下かつValueObjectなクラスなら、structへの変換を検討する
6. ボックス化に注意する
7. unity01ex. プロファイルとメモリリーク の 「メモリリークしやすいポイント」を対応する
8. JsonUtilityのシリアライズ時、nullは勝手にnewされてしまう。加味すること
9. 自作ジェネリッククラスやメソッドで、where制約を忘れないこと（objectボックス化を避ける）

________________________________________
## 2. 対CPUパフォーマンス
________________________________________
高負荷処理を避ける

1. Relase時に自動的に除去されるログラッパーを用意する
2. Regexは結構重い
3. JsonUtilityのデシリアライズは結構重い
4. GameObjectやComponentの探索は便利な反面、探索コストが重い
    - GetComponent系、Find系は可能ならば避けるか、キャッシュすること
    - Findは、現在アクティブなシーン全体からGameObjectを探索する。重い操作になりうる
    - FindWithTag、FindObjectOfType、もFindとほぼ同様
    - FindGameObjectsWithTag、FindObjectsByTypeもFindとほぼ同様
    - GetComponent(s)は、そのGameObjectにアタッチされているComponentを探索する
    - GetComponent(s)InChildrenは、そのGameObjectと子孫全体からComponentを探索する。重い操作になりうる
    - GetComponent(s)InParentは、そのGameObjectと祖先全体からComponentを探索する。重い操作になりうる
5. クリティカルパス（1000回以上のループなど）での検討事項
    - 空のUnityイベント関数は削除
    - TransformをC#側キャッシュ(実環境では5%程度の差なのでクリティカルな場合のみ検討)
    - 固定配列+forの使用を検討する
    - 2020.2以降は、MethodOptions.AggressiveInlining がIL2CPPに対して効くようになった
6. HashSetで要件を満たせる場合、ListやDictionaryより優先する

Unityの機能で重いもの

1. Animatiorは重い。複数Layerまで使うと非常に重い。DOTweenなどで行けるか検討する
2. Layout Groupは重い。可能なら避ける
3. Instantiate/Destroyは重い。オブジェクト使い回しが可能か検討する

________________________________________
## 3. 対起動時間のプラクティス
________________________________________
含まれる内容やロード内容を最適化する

1. Resourceフォルダ内のファイルを多くしすぎない
2. 最低限必要なものをロードしていく