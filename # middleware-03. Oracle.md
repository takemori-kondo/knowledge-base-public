# middleware-03. Oracle
________________________________________
## 1. Install and Settings
________________________________________
### 1.1.A. Install for Windows

```text
1. 公式サイトにログインしてダウンロード
2. setup.exe
    1. セキュリティ：不要
    2. Gridインスト：データーベース・ソフトウェアのみ
    3. インストール：単一インスタンス
    4. 製品言語    ：日本語
    5. エディション：Enterprise
    6. OSユーザ    ：oracle/oracle
    7. インストール：C:\app\oracle\product\12.1.0\dbhome_1
3. Database Configuration Assistant
    1. データベースの作成
    2. デフォルトの構成でデータベースを作成します 選択
        1. DB名      ：dev(単一インスタンスの場合、サービス名、SIDもこれ)
        2. 記憶域    ：FS(ファイルシステム)
        3. 場所      ：oradata
        4. リカバリ  ：fast_recovery_area
        5. 文字コード：AL32UTF8
        4. パスワード：dev
        5. (oracle    ：OSユーザ作成時に指定したパスワード(上記ならoracle))
        6. コンテナ  ：off
4. コントロールパネル > 管理ツール > サービスを確認
    1. 以下二つのサービスが登録されており、起動中か確認
        - OracleService○○
        - Oracle<ORACLE_HOME>TNSListener
    2. TNSListenerがない場合、設定する
        - Net Configuration Assistant
            - リスナー構成
            - 追加
            - LISTENER, パスワード入力
            - TCP
            - 標準ポート番号の1521を使用
        - 再度サービスを確認し、Oracle<ORACLE_HOME>TNSListenerが実行中で追加されたか確認
```

Windows対応

```text
Windows 10 Creators UpdateでSQL*Plusを使うと日本語が文字化けする件について
https://blog.shibata.tech/entry/2017/05/26/220900

Windows(Version 1809)上でコンソール出力が正しく表示されない問題による製品への影響について
http://www.hitachi-support.com/alert/us/HWS19-003/index.htm

1. NLS_LANG=JAPANESE_JAPAN.JA16SJISTILDE
2. 上記設定してもWindows10でsqlplusで文字化けする場合
    - cmdのレガシコンソールを使用する有効化
    - 根本解決する場合：ver. 1703 → KB4020102
    - 根本解決する場合：ver. 1809 → KB4490481
```

Directory (Windowsはレジストリに情報がある)

Win directory   |Path
----------------|---------------------------------------------
Oracle Inventory|c:\Program Files\Oracle\Inventory
ORACLE_BASE     |c:\app\oracle
ORACLE_HOME     |c:\app\oracle\product\<version>\dbhome_1

________________________________________
### 1.1.B. Install for linux

Databaseインストレーション・ガイドfor Linux  
https://docs.oracle.com/cd/E96517_01/ladbi/

Oracle Linux: A better alternative to CentOS  
https://linux.oracle.com/switch/centos/

Note

```text
1. OUI上での設定は、Windowsと同じでOK
2. sqlplusは履歴機能がないのでrlwrap併用で対応。.bash_profileにエイリアス
3. リスナーは起動していない。oracleもOS再起動後は起動していない
4. lsnrctlをstart後にOracle Databaseそのものを再起動しないと、listenerが機能しない
5. Linux版はoracleもlistenerも自動起動が用意されていない
6. ハードウェアや事前要件は以下の通り

20GB storage
 2GB memory
 3GB swap
x window
Oracle Linux互換
```

X window settings

```bash
# e.g. vmhost=windows & vm=linux, tera term & vcxsrv
# install vcxsrv to windows
# start vcxsrv by XLaunch
# tera term menu > setup > ssh forwarding > check x forwarding
# tera term menu > setup > save setup...
# restart tera term
sudo yum -y install xorg-x11-xauth.x86_64 xorg-x11-server-utils.x86_64
sudo yum -y install xeyes
# re-login
xeyes
```

rlwrap

```bash
sudo yum -y install wget
wget http://download.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo rpm -ivh epel-release-latest-7.noarch.rpm
sudo yum -y install epel-release
sudo yum -y install readline-devel
sudo yum -y install rlwrap
```

Centos to oracle linux

```bash
sudo curl -O https://linux.oracle.com/switch/centos2ol.sh
sudo sh centos2ol.sh
sudo yum -y distro-sync
```

Pre install

```bash
sudo yum -y install oracle-database-preinstall-18c
sudo yum update
```

Install

```bash
sudo passwd oracle
# oracle
# Login oracle official page, and download LINUX.X64_180000_db_home.zip
mv /home/vagrant/vagrant_shared/LINUX.X64_180000_db_home.zip /tmp
sudo mkdir -p /u01/app/oracle/product/18.0.0/dbhome_1
sudo chown -R oracle:oinstall /u01
su - oracle

# oracle user
cd /u01/app/oracle/product/18.0.0/dbhome_1
unzip -q /tmp/LINUX.X64_180000_db_home.zip
./runInstaller
# OUI
## Set Up Software Only
## Single instance database installation
## Enterprise Edition
## /u01/app/oracle
## /u01/app/oraInventory, oinstall
## dba, oper, backupdba, dgdba, kmdba, racdba
## install
### ignore all
## If show "Execute Configuration Script@..."
## open another terminal
### sudo /u01/app/oraInventory/orainstRoot.sh
### sudo /u01/app/oracle/product/18.0.0/dbhome_1/root.sh
### Enter the full pathname of the local bin directory: [/usr/local/bin]:
### Do you want to setup Oracle Trace File Analyzer (TFA) now ? yes|[no] :
### exit
## ok
## Close
vi ~/.bash_profile
# # User specific environment and startup programs 
# ORACLE_BASE=/u01/app/oracle
# ORACLE_HOME=/u01/app/oracle/product/18.0.0/dbhome_1
# ORACLE_SID=dev
# NLS_LANG=JAPANESE_JAPAN.AL32UTF8
# PATH=<other_paths><:>$ORACLE_HOME/bin
#
# export ORACLE_BASE
# export ORACLE_HOME
# export ORACLE_SID
# export NLS_LANG
# export PATH
# alias sqlplus='rlwrap -pRed sqlplus'
exit

# vagrant user
exit
```

Create database and run listener

```text
# re-login
su - oracle

dbca
# create a database
## Global database name > dev
## Storage type > File System
## Database file location > {ORACLE_BASE}/ora_data/{DB_UNIQUE_NAME}
## Fast Recovery Area > {ORACLE_BASE}/fast_recovery_area/{DB_UNIQUE_NAME}
## Database character set > AL32UTF8
## Administrative password > dev
## Create as Container database > no check
lsnrctl
# start
# quit
```

Restart Oracle

```text
# re-login
su - oracle

lsnrctl
# start
# quit
sqlplus system/manager as sysdba
# SHUTDOWN IMMEDIATE
# STARTUP
```

Directory

Linux directory |Path
----------------|---------------------------------------------
Oracle Inventory|/u01/app/oraInventory
ORACLE_BASE     |/u01/app/oracle
ORACLE_HOME     |/u01/app/oracle/product/18.0.0/dbhome_1

________________________________________
### 1.2. Add Sample Schema

1. sqlplus system/manager as sysdba
    1. create tablespace foo datafile 'foo.dbf' SIZE 100M;
    2. create user foo identified by "bar" default tablespace foo;
    3. grant unlimited tablespace to foo;
    4. grant resource to foo;
    5. grant connect to foo;
    6. quit
2. sqlplus foo/bar@localhost:1521/dev
    1. create table accounts(id number(18,0), name varchar2(255), primary key(id));
    2. select table_name from user_tables;
    3. insert into accounts values(1, 'アリス');
    4. insert into accounts values(2, 'ボブ');
    5. select * from accounts;
    3. quit

________________________________________
## 2. Sample for .NET
________________________________________
ツール > NuGet パッケージマネージャー > ソリューションのNuGetパッケージの管理

```text
Oracle.ManagedDataAccess を追加（ダウンロードされて追加される）
```

フォームデザイナ

```text
ボタンを配置してダブルクリックして、button1_Clickに移動
```

コード

```cs
using Oracle.ManagedDataAccess.Client;
using System;
using System.Windows.Forms;

namespace WindowsFormsApp1
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            var conn = new OracleConnection();
            conn.ConnectionString = "User ID=foo; Password=bar; Data Source=192.168.3.251:1521/dev";
            conn.Open();
            var cmd = conn.CreateCommand();
            cmd.CommandText = "select * from accounts";
            var reader = cmd.ExecuteReader();
            while (reader.Read())
            {
                var str = "id:" + reader.GetInt32(0).ToString() + Environment.NewLine
                    + "name:" + reader.GetString(1);
                MessageBox.Show(str);
            }
        }
    }
}
```

________________________________________
## 3. Knowledge
________________________________________
### 3.1. DBへのログイン方法

```text
1. ローカル限定 : OS認証（sys扱いになる）
    - sqlplus / as sysdba
2. ローカル限定 : リスナーを使用しない接続
    - sqlplus sys/change_on_install as sysdba
    - sqlplus system/manager as sysdba
    - sqlplus system/<password>
3. 通常の接続
    - sqlplus system/<password>@<ip>:<port>/<sid>
```

________________________________________
### 3.2. デフォルトロール

```text
dba
unlimited tablespace
connect
resource
create view
create synonym
debug any procedure
debug connect session
```

________________________________________
### 3.3. 初期化パラメータ

初期化パラメータ － ＠IT  
https://www.atmarkit.co.jp/fdb/ref/ref_oracle/param.html

サーバパラメータファイル（spfile）

項目    |典型的な例
--------|------------------------------------
Windows |ORACLE_HOME\database\SPFILE<SID>.ora
Linux   |ORACLE_HOME\dbs\SPFILE<SID>.ora

※1 旧形式として、pfileがある。非推奨 

各種確認

項目       |典型的な例
-----------|------------------------------------
spfileか   |SELECT value FROM v$parameter WHERE name = 'spfile';
spfile内容 |SELECT * FROM v$spparameter WHERE value IS NOT NULL;
spfile変更 |ALTER SYSTEM SET <name> = '...' SCOPE=SPFILE;

※ pfileの場合、ALTER SYSTEMではファイルは書き変わらない

________________________________________
### 3.4. 文字コード、NLS、長さセマンティクス

Databaseグローバリゼーション・サポート・ガイド  
https://docs.oracle.com/cd/E96517_01/nlspg/index.html  

要約

```text
1. 文字コード自体は、以下の3つの観点のみ
    1. database値。DB作成後に変更できない
        - nls_database_parameters.nls_characterset
        - nls_database_parameters.nls_nchar_characterset
    2. NLS_LANG値。クライアントのレジストリや環境変数
    3. database値とクライアント値が異なるとき、自動変換される
2. NLSは以下の3段階を持つ
    - nls_database_parameters
    - nls_instance_parameters
    - nls_session_parameters
    - ※ v$nls_parameters ならsession+dbの2つ値を確認できる
3. char、varchar2型は長さセマンティクスにcharかbyteかを選べる
    - 初期化パラメータ、レジストリ、環境変数にないなら、Byteがデフォルト
4. 3に関わらず、char、varchar2、nchar、nvarchar2の内部上限は4000Byte
```

________________________________________
### 3.5. Frequency Operations

```sql
-- 3. Way to list up
-- SELECT username, lock_date, expiry_date, default_tablespace, created, default_collation FROM dba_users WHERE oracle_maintained<>'Y'AND username<>'EMULATION' ORDER BY username;
-- SELECT owner, table_name, tablespace_name, num_rows, last_analyzed, default_collation FROM all_tables WHERE owner = 'FOO' ORDER BY  owner, table_name;
-- SELECT file_name, tablespace_name, round(bytes / 1073741824, 1) AS bytes_gb, autoextensible, round(maxbytes / 1073741824, 1) AS maxbyte_gb FROM dba_data_files ORDER BY file_name;
-- SELECT * FROM dba_temp_files;
-- 
-- SELECT * FROM dba_sys_privs WHERE grantee = 'DBA';
-- SELECT * FROM dba_sys_privs WHERE grantee IN ('CONNECT', 'RESOURCE') ORDER BY grantee;
/*
SELECT
 privs.*
FROM
(
    SELECT grantee, privilege, NULL AS granted_role, admin_option FROM dba_sys_privs
    UNION ALL
    SELECT grantee, NULL AS privilege, granted_role, admin_option FROM dba_role_privs
) privs
 WHERE privs.grantee = 'MWREP';
*/
--
-- 4. Way to remove undo tablespace
-- -- 表領域のDATAFILEは、指定しなければOracle Managed Filesになり、表領域削除時に勝手に消える。
-- CREATE UNDO TABLESPACE undotbs2
-- ALTER SYSTEM SET undo_tablespace = 'UNDOTBS2';
-- -- システム管理表領域＆データファイルは INCLUDING CONTENTS AND DATAFILES; が無くてもよい。
-- DROP TABLESPACE undotbs1;
-- CREATE UNDO TABLESPACE undotbs1;
-- ALTER SYSTEM SET undo_tablespace = 'UNDOTBS1';
-- DROP TABLESPACE undotbs2;
--
-- 5. Way to remove temporary tablespace
-- CREATE TEMPORARY TABLESPACE tempdummy;
-- ALTER DATABASE DEFAULT TEMPORARY TABLESPACE tempdummy;
-- DROP TABLESPACE temp;
-- CREATE TEMPORARY tablespace temp;
-- ALTER DATABASE DEFAULT TEMPORARY TABLESPACE temp;
-- コントロールパネル > 管理ツール > サービス > OracleServiceXXX再起動
-- DROP TABLESPACE tempdummy;
-- 
-- 6. Way to remove migration user & tablespace & datafile
-- DROP USER mwrep CASCADE;
-- ALTER DATABASE DATAFILE 'C:\APP\ORACLE\ORADATA\DEV\DATAFILE\O1_MF_USERS_GQGOF8BV_.DBF' RESIZE 50M;
--
-- 7. Way to create tablespace & user
-- CREATE TABLESPACE foo DATAFILE 'foo.dbf' SIZE 100M AUTOEXTEND ON NEXT 100M;
-- CREATE USER foo IDENTIFIED BY foo DEFAULT TABLESPACE foo TEMPORARY TABLESPACE temp;
-- GRANT connect, resource, CREATE VIEW, UNLIMITED TABLESPACE TO foo;

-- 8. Way to create tablespace & strong user
-- CREATE TABLESPACE mwrep DATAFILE 'mwrep.dbf' SIZE 100M AUTOEXTEND ON NEXT 100M;
-- CREATE USER mwrep IDENTIFIED BY mwrep DEFAULT TABLESPACE mwrep TEMPORARY TABLESPACE temp;
-- GRANT connect, resource, dba, CREATE VIEW, UNLIMITED TABLESPACE TO mwrep;
--
-- 9. Way to Move datafile
-- ALTER TABLESPACE USERS OFFLINE;
-- エクスプローラでファイル移動
-- ALTER TABLESPACE USERS RENAME DATAFILE 'C:\APP\ORACLE\ORADATA\DEV\DATAFILE\O1_MF_USERS_GQGOF8BV_.DBF' TO 'D:\oracledatafile\O1_MF_USERS_GQGOF8BV_.DBF'
-- ALTER TABLESPACE USERS ONLINE;
--
-- 10. Way to Add datafile to tablespace (1datafile maxsize is 4194303(2^22 - 1) probrem)
-- ALTER TABLESPACE USERS ADD DATAFILE 'D:\oracledatafile\USERS2.DBF' SIZE 30G AUTOEXTEND ON NEXT 100M;
--
-- 11. Way to drop datafile from tablespace
-- ALTER TABLESPACE USERS drop DATAFILE 'D:\oracledatafile\USERS2.DBF';
--
-- 12. Way to reanalyze (sqlplus)
/*
BEGIN
  DBMS_STATS.GATHER_SCHEMA_STATS (
    OWNNAME => 'FOO'
    ,OPTIONS => 'GATHER'
  );
END;
/
*/
-- 13. Way to get last compiled date for procedure
/*
SELECT
  p.object_name
  , p.object_type
  , p.procedure_name
  , o.last_ddl_time
  , o.status 
FROM
  user_procedures p
  , user_objects o 
WHERE
  p.object_name = o.object_name
  AND o.last_ddl_time >= '2019/12/10 20:00:00'
ORDER BY
  last_ddl_time
*/
```

________________________________________
### 3.6. TNS check & settings

リスナーチェック

1. from client pc
    - ping <ip>
    - tnsping.exe <ip>
2. on server pc
    - lsnrctl status
        - もしだめなら、Oracle Net Configuration Assistant
           1. リスナーの構成
           2. 追加
           3. LISTENER

クライアント側でtns使う場合

1. from client pc
    1. Oracle Net Configuration Assistant
        1. ネーミングメソッドの構成
        2. ローカルネーミング（クライアントPCのtnsnames.oraで接続する）
    2. Oracle Net Manager　あるいは　Oracle Net Configuration Assistant > ローカルネットサービス名構成
        1. サービス・ネーミング
        2. 追加
        3. foo
        4. tcp/ip
        5. 192.168.3.251/1521
        6. dev
        7. テスト(foo/barで)
        8. 保存

________________________________________
### 3.7. その他Oracle固有問題

識別子

```text
- 識別子をすべて大文字にする
- ""で囲まれた識別子はそのままにする
```

Sqlplus実行

```text
- 文末;
- PL/SQLブロック終了/ 実行開始
```

MaterializedViewのリフレッシュ

```sql
-- SQLPlusから下記実行
execute dbms_mview.refresh('<MVの名前>');
```

________________________________________
## 4. Hierarchical Queries
________________________________________
Sample

```query
CREATE TABLE "KAISO_TEST" 
(
	"ID" NUMBER(*,0), 
	"CATEGORY" VARCHAR2(3), 
	"CODE" NUMBER(*,0), 
	"OYA_CODE" NUMBER(*,0), 
	 PRIMARY KEY ("ID")
);

INSERT INTO
  KAISO_TEST
VALUES
  (1, A, 1, NULL),
  (2, A, 2, 1),
  (3, A, 3, 1),
  (4, A, 4, 3),
  (5, A, 5, 3),
  (6, A, 10, NULL),
  (7, A, 11, 10),
  (8, B, 1, NULL),
  (9, B, 2, 1),
  (10, B, 3, 1),
  (11, B, 4, 3),
  (12, B, 5, 3),
  (13, B, 10, NULL),
  (14, B, 11, 10);

/* 
【データの内容】
次のような構造が、category='A', category='B' 両方である（階層はCODE/OYA_CODE）
        1   10
       /|    |
     2  3   11
       /|
      4 5

【メモ】
WHEREは階層処理が終わった後に対して実行される
つまり、CONNECT BY に親子のカテゴリが同一であるという接続条件を指定する必要あり
*/

SELECT
    id,
    category,
    code,
    oya_code,
    CONNECT_BY_ROOT code AS root_code
FROM
    kaiso_test
WHERE
    category='A'
START WITH
    oya_code IS NULL
CONNECT BY
        PRIOR code      = oya_code 
	AND PRIOR category  = category
ORDER BY
    root_code, id
```
