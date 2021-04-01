# eco-10. cmd.exe
________________________________________
## 1. バッチ知識
________________________________________
### 1.1. 環境設定

右クリック > 編集 を任意のエディタに変更

```text
HKEY_CLASSES_ROOT
  batfile
    shell
      edit
        command
          既定

例えばインストール版なら下記の通り
%ProgramFiles(x86)%\sakura\sakura.exe %1
```

________________________________________
### 1.2. リファレンス

リファレンス

https://www.pg-fl.jp/program/dos/doscmd/

注意点

```text
1. インデントはOK
2. = や > で空白を入れない（誤動作の原因になりがち）
3. 弱いクォートは変数展開される
4. 終了コードはerrorlevel目的。戻り値として使用してはいけない

* いずれも、bashも同じ考え方
```

bashと比較1

操作                   |bash            |cmd.exe
-----------------------|----------------|--------------------------------
パス区切り             |/               |バクスラ
wild card 01           |なし            |?
wild card 1            |?               |なし
wild card 0n           |*               |*
wild card（クラス）    |[a-zA-Z], [!0-9]|なし
ホームディレクトリ     |~               |%HOMEPATH%
カレントディレクトリ   |pwdコマンド     |~
バッチディレクトリ     |頑張る          |%~dp0
コメント               |#               |rem
入力リダイレクト       |<               |<
出力リダイレクト       |>               |>
追記                   |>>              |>>
破棄                   |>/dev/null      |>nul
空ファイル上書き       |:>ファイル名    |type nul>ファイル名
弱いクォート           |"               |"
エスケープ             |バクスラ        |バクスラ
echo on                |set -x          |デフォルト
echo off               |デフォルト      |@echo off
変数参照               |${var}          |%var%
シェル変数             |export VAR="foo"|set var=""
遅延評価モード         |デフォルト      |setlocal ENABLEDELAYEDEXPANSION
変数参照（遅延）       |デフォルト      |!変数!
コマンド文字列         |$0              |%0
コマンド引数           |$1, ...         |%1, ...
終了コード（※2）      |return 0        |exit /b

-

bashと比較2（シェル変数と引継ぎの仕様）

シェル|操作         |process   |シェル変数 |引数 |備考
------|-------------|----------|-----------|-----|------------------------------------
cmd   |call         |同じ      |共有       |独立 |setlocalもサブには共有される
cmd   |start        |異なる    |コピー     |独立 |処理を待たない
cmd   |直接         |同じ      |共有       |独立 |元バッチの後続処理は実行されない（goto的）
bash  |export source|同じ      |同じ       |同じ |サブシェルの引数0が置き変わらない
bash  |export bash  |異なる    |コピー     |独立 |ー
bash  |source       |同じ      |同じ       |同じ |サブシェルの引数0が置き変わらない
bash  |bash         |異なる    |孤立       |独立 |ー

-

（※）setlocal ENABLEDELAYEDEXPANSIONと、export bashがほぼ同一の挙動になる

________________________________________
## 2. サンプル
________________________________________
バッチサンプル

```bat
@echo off

setlocal enabledelayedexpansion

rem setlocal enabledelayedexpansion : 遅延評価モード有効化。bashと違いcmdは通常の変数解決がステートメント開始時のため
rem >>                              : 出力インデント（追記）
rem set A=B                         : シェル変数設定。=の間に空欄を含めてはならない
rem %~dp0                           : %0に対する、クォートなしドライブ+パス
rem pushd                           : popdで元のディレクトリに戻れるcd
rem for /f %%i                      : ファイル読み込みfor。%%iは、1文字でないといけない
rem :FOO                            : ラベル。gotoまたはcallコマンドの飛び先
rem call                            : 別バッチ呼出 or ラベル呼出
rem exit /b                         : 別バッチ戻り or ラベル戻り。終了コードはerrorlevel


rem :MAINとインデントは関数っぽく見せるために記載してるだけ。無くても問題ない
:MAIN

    rem カレントディレクトリを保持
    set CUR_DIR=%~dp0
    
    pushd %CUR_DIR%
    
    rem 必要なフォルダ、ファイルの初期化
    set CONFIG_DIR=%CUR_DIR%Config\
    set LOG_DIR=%CUR_DIR%Log\
    if not exist %CONFIG_DIR% (mkdir %CONFIG_DIR%)
    if not exist %LOG_DIR% (mkdir %LOG_DIR%)
    
    set SRCLIST_FILE=%CONFIG_DIR%srclist.txt
    set LOG_FILE=%LOG_DIR%%~n0.log
    if not exist %SRCLIST_FILE% (
        echo ダミー行１>>%SRCLIST_FILE%
        echo ダミー行２>>%SRCLIST_FILE%
    )
    if not exist %LOG_FILE% (type nul>%LOG_FILE%)
    
    rem バッチ開始
    echo [%time%]バッチ開始。>>%LOG_FILE%
    
    rem ファイルを読み込んで処理
    echo [%time%]ファイル読み取り開始：%SRCLIST_FILE%>>%LOG_FILE%
    for /f %%i in (%SRCLIST_FILE%) do (
        echo 行内容：%%i>>%LOG_FILE%
        if %%i=="" (
            echo 設定ファイルに空行があります。>>%LOG_FILE%
            goto :END
        )
        call :FUNC_FOO %%i
    )

    rem バッチ終了
    :END
    echo [%time%]バッチ終了。>>%LOG_FILE%

exit /b





rem FUNC_FOO
:FUNC_FOO

    echo FUNC_FOO開始：%1>>%LOG_FILE%
    echo FUNC_FOO終了。>>%LOG_FILE%

exit /b
```