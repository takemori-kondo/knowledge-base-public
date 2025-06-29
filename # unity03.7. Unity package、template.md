# unity03.7. Unity package、template
________________________________________
Unity の Package Manager  
https://docs.unity3d.com/ja/2022.3/Manual/Packages.html

パッケージレイアウト  
https://docs.unity3d.com/ja/2022.3/Manual/cus-layout.html

カスタムパッケージの作成  
https://docs.unity3d.com/ja/2022.3/Manual/CustomPackages.html
________________________________________
## 1. .unitypackageとUPM
________________________________________
.unitypackage

- レガシーで単純な仕組み
- exportした内容がそのままimportされる
    - Assets > Export package... でexport、export対象を選択

UPM

- 現在の主流
- UPM対応パッケージは、gitあるいはサードパーティUPM Registry（OpenUPMなど）で公開する
- 利用側はimportすると、DLされてPackages下に表示される
    - 利用側がパッケージを改変することを想定していない
- 公開側は、package.jsonで依存関係を記述可能
   - ただし解決可能なのはUPM Registryで公開されているパッケージのみ
   - また、利用側がサードパーティUPM Registryを使うには、Scoped Registryに追加が必要

補足

- どちらも動作は単純で、単にフォルダを丸ごと追加コピペする、とほぼ同様
    - UPMの場合は、実際には圧縮してDLされたものがキャッシュされている

________________________________________
## 2. UPM
________________________________________
### 2.1. 概要

パッケージマニフェスト - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/upm-manifestPkg.html

UPM対応の要点

1. パッケージするファイル群は予め特定のフォルダ以下のみにしておく
2. そのフォルダのルートに、package.json(パッケージマニフェスト)を追加すればOK

package.jsonのキーの意味

キー        |意味や利用のされ方
------------|----------------------------------
name        |UPMで実際に識別に利用される名前。キャッシュのフォルダ名。逆ドメイン形式推奨
displayName |エディタ上で表示される名前。Package Manager、ProjectウィンドウのPackagesなど
version     |メジャー.マイナー.パッチ。互換性のない変更がある場合はメジャーを上げること
unity       |動作保証の最低バージョン
description |Package Manager上でdescriptionとして表示される内容
author      |Package Manager上でauthorとして表示される内容
dependencies|依存関係を自動解決するための記述。OpenUPM（設定した場合）やUnity Registryに対してのみ可能
license     |MITなど

.

________________________________________
### 2.2. importの詳細挙動

git経由の場合

- .git?path=foo以下が圧縮DLされる
- DLされた内容は、個々のプロジェクトフォルダ内にキャッシュされる

UPM Registry経由の場合

- 登録した内容が圧縮DLされる
- DLされた内容は、%USERPROFILE%\AppData\Unity内にキャッシュされる

________________________________________
### 2.3. UPMパッケージを作る際の推奨構成

1. 1プロジェクト1UPMパッケージ
2. 下記パッケージレイアウトを参考に、ある程度自由でよい
3. サンプルは、開発中とUPM側の挙動を考慮すると、Samples~ + Samplesシンボリックリンクが現実的
    - ただしこの場合、VisualStudio上でのGitステータスが正常に表示されなくなる

パッケージレイアウト  
https://docs.unity3d.com/ja/2022.3/Manual/cus-layout.html


- Assets
    - [org-name.package-name]
        - Runtime
            - [org-name.package-name].asmdef
        - Samples~
            - XXXSample
                - [org-name.package-name].XXXSample.asmdef
            - XXXSample
            - ...
        - Tests/Editor
            - XXX
        - package.json
        - README.md
    - Samples (Samples~ へのシンボリックリンク)

名前空間やasmdefの補足

- Runtimeは省略
- それ以外は素直に構成

________________________________________
### 2.4. Windows下のSamples~とシンボリックリンク

1. 開発時はSamples~下のassetをエディタで編集できた方が都合がいい
    - Samples~に対してAssets\Samplesとしてシンボリックリンクを作成すれば良さげ
2. Windowsのgitはシンボリックリンクを復元しないので、クローン時やリセット時のgit上での扱いは注意
    - シンボリックリンク削除&再作成でローカルとしては解決可能

```powershell
# 管理者権限で起動
$dir = "Uft.UnityUtils"
cd "D:\Dropbox\repos_unity\$dir\Assets\"
New-Item -ItemType SymbolicLink -Path ".\Samples" -Target ".\$dir\Samples~"
```