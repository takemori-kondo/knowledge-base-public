# eco-04. DB Tools
________________________________________
## 0. List
________________________________________
DB Tools

Tool                |isFree|DB Viewer|ER2DB|DB2ER|ER Edit|PL/SQL Edit|SQL format
--------------------|------|---------|-----|-----|-------|-----------|----------
A5:SQL Mk2          |yes   |yes      |yes  |yes  |yes    |-          |poor
MySQL Workbench     |yes   |yes      |yes  |yes  |yes    |-          |poor
Oracle SQL Developer|otn   |-        |yes  |yes  |yes    |-          |poor
ER Master           |yes   |-        |yes  |yes  |yes    |-          |-
PL/SQL Developer    |no    |yes      |-    |-    |-      |yes        |yes

-

DB Advanced Tools

Tool                            |Command
--------------------------------|-------
Microsoft Command Line Utilities|bcp
SQL*Loader                      |sqlldr

-

________________________________________
## 1. A5:SQL Mk2
________________________________________
### 1.1. 接続の変更や調整方法

接続

```text
「データベースの追加と削除」から行う
```

設定 > オプション

```text
※ デフォルトに戻すは機能しない

結果セット　> 結果セット最大行数　 > 160000
SQL　　　　 > 実行位置　　　　　　 > 先頭から全て
SQL　　　　 > SQLファイルのデフ…  > UTF8
SQL　　　　 > SQL実行ログ　　　　　> チェックしない
SQLエディタ > オートブラケット　　 > チェック
SQLエディタ > タブの扱い　　　　　 > 空白
SQL整形　　 > キーワードの大文字… > 大文字
SQL整形　　 > 識別子の大文字…　　 > 小文字
SQL整形　　 > カンマの位置　　　　 > 式の前
SQL整形　　 > SELECT句内のカラム… > チェック
SQL整形     > インデント桁数       > 4
SQL整形　　 > 1行コメントの位置　　> 1桁以降
Oracle　　　> NLS_LANG　　　　　　 > JAPANESE_JAPAN.AL32UTF8
```

________________________________________
### 1.2. 定義書出力(要Excel)

1. Excelがインストールされていること
2. dbを開く
3. データベース > テーブル定義書作成
4. しばらく待つ

________________________________________
### 1.3. ER > DB / DB > ER

どちらも可能

________________________________________
### 1.4. バージョン補足

Oracle直接接続で必要なバージョン

- 12c Release 2 は、2.13系以降が必要

________________________________________
## 2. MySQL Workbench
________________________________________
### 2.1. DB > ER

1. あらかじめDB Serverへの接続を登録しておく
2. Database > Rever se Engeer
3. 接続を選択 > Next
4. Next
5. ER出力したいDBを選択 > Next
6. Next
7. Execute
8. MySQL Modelタブ > mydbを削除
9. 保存

________________________________________
### 2.2. ER > DB(DROP & CREATE)

※ 単にDrop & Createするだけです。既存のデータは消えます。

1. 「1.1. DB > ER」を済ませておく
2. 適当にERを変更
3. Database > Forword
4. 以下のようにする
    - 「DROP objects before each CREATE object」にチェック
    - 「Generate DROP SCHEMA」にチェック
    - Next
5. Next
6. Next
7. Close

________________________________________
### 2.3. New ER > New DB

1. あらかじめDB Serverへの接続を登録しておく
2. File > New Model
3. Add Diagram
4. 色々作成
5. Database > Forword。以下「1.2. ER > DB(DROP & CREATE)」と同じ手順でOK

________________________________________
## 3. Oracle Sql Developer
________________________________________
### 3.1. SQL Server → Oracle移行を行う

想定環境

```text
Oracle SQL Developer ver.19.1.0.094
Oracle Database 12c Enterprise Edition 12.2.0.1.0
Microsoft SQL Server 2017 (RTM) - 14.0.1000.169 (X64)
JTDS（サードパーティJDBC for SQL Server。MS公式JDBCでは動かないため）
    - http://jtds.sourceforge.net/
    - Sql developer > Preference > Database > third party > jar追加
```

下記を参考に移行操作する

```text
Microsoft SQL ServerからOracle Database 11gへの移行
https://www.oracle.com/webfolder/technetwork/jp/obe/db/hol08/sqldev_migration/mssqlserver/migrate_microsoft_sqlserver_otn.htm

Microsoft SQL ServerからOracle Database 11gへの移行（オンライン接続）
https://www.oracle.com/webfolder/technetwork/jp/obe/db/hol08/sqldev_migration/mssqlserver/viewlets/sqlserver_online.html

※ 想定環境の場合、細かい操作は異なる。以下の通り。

1. mwrepには、unlimited tablespace、dba権限が必要
2. 移行先Userも予め作成する必要がある。systemでログインして例えば以下の通り

create user migration_dest identified by "migration_dest";
grant connect, resource, CREATE SESSION, CREATE VIEW, unlimited tablespace, dba to migration_dest;

3. キャプチャ、コンバートの2手順ではなく、移行操作でワンセットに整理された
    1. 移行リポジトリ、切捨て（前回移行情報の削除するか）の指定(mwrep)
    2. captureするrepositoryと関連付けられているuser、およびログ等出力先
    3. モードと移行元の接続を指定
    4. 移行元dbの指定
    5. 変換オプションの指定
    6. テーブル以外の移行スキーマの指定
    7. 移行先へのDDL生成と実行の接続を指定(<foo>)
    8. データ移行の移行元、移行操作に使用する接続の指定(<foo>)
    9. 最終確認
    
以下完全手順
1. 移行先に十分な容量を確保する（移行元のデータ量が60GBなら移行先を120GB程度確保）
2. 移行元・移行先の電源設定を設定して、勝手に停止しないようにする
3. 移行元・移行先のウイルス対策ソフトのネットワークをオフにして、はじかれないようにする
4. system/<Oracle作成時のパスワード> の接続を追加し、接続
5. CREATE TABLESPACE mwrep DATAFILE 'mwrep.dbf' SIZE 100M AUTOEXTEND ON NEXT 100M;
6. CREATE USER mwrep IDENTIFIED BY mwrep DEFAULT TABLESPACE mwrep TEMPORARY TABLESPACE temp;
7. GRANT connect, resource, dba, CREATE VIEW, UNLIMITED TABLESPACE TO mwrep;
8. 切断。
9. mwrep/mwrep の接続を追加し、接続
10. mwrep右クリック > 移行リポジトリ > 移行リポジトリの関連漬け 
11. 十分な容量がない場合、undo, temp, usersデータファイルをDドライブにするなど。
    1. Oracle Managed FilesにDataFileの作成位置やファイル名を任せる場合
        1. ALTER TABLESPACE UNDOTBS1 ADD DATAFILE; -- 例：120GB必要なら、3回（1ファイル辺り約30GBが限界サイズなため、もともとあるファイル+3つ）
        2. ALTER TABLESPACE temp ADD TEMPFILE; -- 例：120GB必要なら、3回（1ファイル辺り約30GBが限界サイズなため、もともとあるファイル+3つ）
        3. ALTER TABLESPACE users ADD DATAFILE; -- 例：120GB必要なら、4回（1ファイル辺り約30GBが限界サイズなため、かつ余裕を持たせたいため）
    2. DataFileを明示する場合
        1. CREATE UNDO TABLESPACE undotbsdummy DATAFILE 'D:\oracledatafile\undotbs_dummy.dbf' SIZE 100M AUTOEXTEND ON NEXT 100M;
        2. ALTER SYSTEM SET undo_tablespace = 'UNDOTBSDUMMY';
        3. DROP TABLESPACE undotbs1;
        4. CREATE UNDO TABLESPACE undotbs1 DATAFILE 'D:\oracledatafile\undotbs1.dbf' SIZE 100M AUTOEXTEND ON NEXT 100M;
        5. ALTER TABLESPACE undotbs1 ADD DATAFILE 'D:\oracledatafile\undotbs2.DBF' SIZE 100M AUTOEXTEND ON NEXT 100M;
        6. ALTER TABLESPACE undotbs1 ADD DATAFILE 'D:\oracledatafile\undotbs3.DBF' SIZE 100M AUTOEXTEND ON NEXT 100M;
        7. ALTER TABLESPACE undotbs1 ADD DATAFILE 'D:\oracledatafile\undotbs4.DBF' SIZE 100M AUTOEXTEND ON NEXT 100M;
        8. ALTER SYSTEM SET UNDO_TABLESPACE = 'UNDOTBS1';
        9. DROP TABLESPACE undotbsdummy;
        10. CREATE TEMPORARY TABLESPACE tempdummy TEMPFILE 'D:\oracledatafile\dummy.dbf' SIZE 100M AUTOEXTEND ON NEXT 100M;
        11. ALTER DATABASE DEFAULT TEMPORARY TABLESPACE tempdummy;
        12. コントロールパネル > 管理ツール > サービス > OracleServiceを再起動
        13. DROP TABLESPACE temp;
        14. CREATE TEMPORARY TABLESPACE temp TEMPFILE 'D:\oracledatafile\temp1.dbf' SIZE 100M AUTOEXTEND ON NEXT 100M;    
        15. ALTER TABLESPACE temp ADD TEMPFILE 'D:\oracledatafile\temp2.DBF' SIZE 100M AUTOEXTEND ON NEXT 100M;
        16. ALTER TABLESPACE temp ADD TEMPFILE 'D:\oracledatafile\temp3.DBF' SIZE 100M AUTOEXTEND ON NEXT 100M;
        17. ALTER TABLESPACE temp ADD TEMPFILE 'D:\oracledatafile\temp4.DBF' SIZE 100M AUTOEXTEND ON NEXT 100M;
        18. ALTER DATABASE DEFAULT TEMPORARY TABLESPACE temp;
        19. コントロールパネル > 管理ツール > サービス > OracleServiceを再起動
        20. DROP TABLESPACE tempdummy;
        21. ALTER TABLESPACE users OFFLINE;
        22. ファイルを移動
        23. ALTER TABLESPACE users RENAME DATAFILE 'C:\APP\ORACLE\ORADATA\DEV\DATAFILE\O1_MF_USERS_GQGOF8BV_.DBF' TO 'D:\oracledatafile\O1_MF_USERS_GQGOF8BV_.DBF'; -- ファイル名は自動生成で異なるため、適切にすること
        24. ALTER TABLESPACE users ONLINE;
        25. ALTER TABLESPACE users ADD DATAFILE 'D:\oracledatafile\USERS2.DBF' SIZE 30G AUTOEXTEND ON NEXT 100M;
        26. ALTER TABLESPACE users ADD DATAFILE 'D:\oracledatafile\USERS3.DBF' SIZE 10G AUTOEXTEND ON NEXT 100M;
        27. ALTER TABLESPACE users ADD DATAFILE 'D:\oracledatafile\USERS4.DBF' SIZE 10G AUTOEXTEND ON NEXT 100M;
        28. ALTER TABLESPACE users ADD DATAFILE 'D:\oracledatafile\USERS5.DBF' SIZE 10G AUTOEXTEND ON NEXT 100M;
        29. Oracleデータベースを再起動
12. 移行先のユーザー作成
    1. CREATE USER <foo> IDENTIFIED BY <foo>;
    2. GRANT connect, resource, dba, CREATE VIEW, UNLIMITED TABLESPACE TO <foo>;
13. 切断
14. <foo>接続を追加
15. 移行元SQL Serverの接続を追加（JTDSが適切に参照できていればできる）
16. 移行したいDBを右クリック > Oracleへ移行
17. 上記要約の3の手順の通りに設定して開始
```

________________________________________
## 4. ER Master
________________________________________
### 4.1. 概要

特長

```text
ER作成に特化したツール。ER設計の補助機能がある
- 名前の自動管理（列名変更時などに一括変更できる）
- カテゴリ分けして別ペイン表示
```

インストール

```text
公式サイトを参照の事（Eclipce必要）
```

フォルダ構成

```text
- workspace-root
  - project-root
    - foo.erm
    - .project
  - .metadata
```

________________________________________
### 4.2. 使い方

```text
ER新規　　　　 : よしなに
SQL生成　 　　 : 右クリック > エクスポート > DDL

ERリバース生成
- JDBCドライバが必要（Thinでよい）
- 新規erm用意 > ER図内を右クリック > インポート > データベース
```

________________________________________
## 5. Microsoft Command Line Utilities (bcp)
________________________________________
インストール（以下すべて必要）

https://feedback.azure.com/forums/908035-sql-server/suggestions/36132202-microsoft-command-line-utilities-15-for-sql-server

- ODBC Driver 13 for SQL Server
- ODBC Driver 17 for SQL Server
- Microsoft Command Line Utilities 15.0 for SQL Server (x64)

バッチサンプル

```bat
rem @echo off
rem DIR類は末尾の\をつけること。ついている前提にすること

SETLOCAL ENABLEDELAYEDEXPANSION
SET CUR_DIR=%~dp0

SET HOST=localhost
SET DATABASE=FOO
SET SCHEMA=dbo
SET DS=%DATABASE%.%SCHEMA%
SET TABLE=%1
SET CONNECT_SEC=-U user_id -P user_pass

SET DATA_DIR=%CUR_DIR%data\
SET  LOG_DIR=%CUR_DIR%logs\

SET EXPORT_WHERE=ADD_DATE > '2011-12-31 23:59:59'

SET DATA_FILE_EXT=.dat
SET EXPORT_OPT=-c -t "`^" -r "`\n" -k


SET EXPORT_LOG_FILE=export.log



bcp "SELECT * FROM %DS%.%TABLE% WHERE %EXPORT_WHERE%" queryout %DATA_DIR%%TABLE%%DATA_FILE_EXT% %EXPORT_OPT% -S %HOST% %CONNECT_SEC% > %LOG_DIR%%TABLE%%EXPORT_LOG_FILE%
bcp %DS%.%1 out %DATA_DIR%%1%DATA_FILE_EXT% -S %HOST% %CONNECT_SEC% %EXPORT_OPT% > %LOG_DIR%%1%EXPORT_LOG_FILE%
```

________________________________________
## 6. SQL*Loader(sqlldr)
________________________________________
使い方（SQL*Loaderは、Databaseに付属する）

https://www.shift-the-oracle.com/utility/sqlloader/

バッチサンプル

```bat
sqlldr %ORA_DS% control='%CTL_DIR%%1%CTL_FILE_EXT%' log='%LOG_DIR%%1%IMPORT_LOG_FILE%' bad='%BAD_DIR%%1%IMPORT_BAD_FILE%' %IMPORT_OPT%
```

-