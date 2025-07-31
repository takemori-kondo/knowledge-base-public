# unity01ex5. android
________________________________________
## 0. IL2CPP問題

スクリプトの制限  
https://docs.unity3d.com/ja/2022.3/Manual/ScriptingRestrictions.html

Why is Linq.Contains being code stripped?  
https://discussions.unity.com/t/il2cpp-why-is-linq-contains-being-code-stripped/837220

IL2CPPはLINQ関連コードがビルド時にstrip(除去)されて実行時MethodAccessExceptionになりがち

- 例：配列とIEnumerable\<T>の互換性がなくなくなり、MethodAccessException

________________________________________
## 1. ビルドとインストール

After Update to Android 14: “This app isn’t compatible with latest version of Android.”  
https://discussions.unity.com/t/after-update-to-android-14-this-app-isnt-compatible-with-latest-version-of-android/943735

1. ビルド設定をandroidにターゲット切り替え
    - 必要なコンパイラ等がない場合は一旦Unity Hub経由でインストール&再起動
2. プレイヤー設定
    - 解像度と表示
        - 解像度スケーリング > 修整したDPI。163～326ぐらいがおすすめ。240がandoridだと1つの区分
            - com.unity.toonshaderなどはDPIに応じて1ドットのアウトラインを引くため、粗すぎると変になりがち
        - Orientation > 横固定したいならLandscape Left
    - Other Settings（android14以降必須）
        - Package Name : 適切にしないとインストールに失敗することもあり
            - Overrideをオフにすれば、Commpany NameとProduct Nameを繋げたものになる
        - Configuration > Scripting backend > IL2CPP
        - Configuration > Target Architectures > ARM64をチェック
        - Active Input Handler : どちらか片方にする必要あり
3. 最低APIレベルは22でもOK
4. 別途Android NDKのダウンロードが必要（IL2CPPに対して必須）
    - Preferences > External Tools > Android > Android NDK
    - Unityのバージョンによって必要なNDKが異なるため注意。
        - 2021.3.38f1ならr21d
        - 6000.0.33f1ならr23b

インストール

1. USB接続-ファイル転送
2. apkコピー
3. タップでインストール可能 (端末を開発者モードにする必要もない)

________________________________________
## 2. 実機USB接続デバッグ方法

1. 端末側設定
    - 開発者向けオプション有効化（設定 > ビルド番号を7回タップ）
    - USBデバッグ有効化（設定 > システム > 開発者向けオプション > USBデバッグ） 
    - PCに接続し、信頼
    - 設定 > 機器接続 > USB > ファイル転送
2. PC側
    - Android Debug Bridgeコマンドラインツールをインストール
    - adb kill-server
    - adb start-server
    - adb devices
        - 接続した端末がリストアップされればOK
3. Unity側
    - Androidビルドに成功していること
    - Build Profiles > Run Deviceに表示されるか確認（Unity再起動必要かも）
    - Development Build ON
    - Script Debugging ON
    - Wait For Managed Debugger ON
4. Build & Run実施
    - Build & Run開始
    - Android側でDebuggerをアタッチしろとポップアップが出るのでVSを開く
        - Debug > Unity デバッガーにアタッチ > Android Player (USB)

成功すると、実機の処理をVSでブレークポイントできる

apkとソースに差異がなければ、接続状態でアプリ起動すれば再度デバッグ可能

________________________________________
## 3. 解像度とSafe Area

UIと解像度(Canvas Scalerの仕様)

- UIスケールモード > 画面サイズに拡大
- 参照解像度 > 適切に調整
- スクリーンマッチモード > デザインの方針に応じて調整

Safe Areaの方針

- 例えば以下
    - https://qiita.com/kiku09020/items/7c4a66d6c8ea7c500a09
- 適当でいいなら、上7%、下4%で十分