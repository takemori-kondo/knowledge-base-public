# php-03. Phalcon
________________________________________
Phalcon Framework  
https://phalcon.io/en-us
________________________________________
## 1. インストール
________________________________________
### 1.1. インストール

Installation - Phalcon Documentation  
https://docs.phalcon.io/latest/installation/

インストールの確認

```bash

# cli側
php -m | grep phalcon
# あるいは、phpinfoを表示させてphalconがある
```

________________________________________
### 1.2. Webサーバの設定

Webサーバのセットアップ - nginx  
https://docs.phalcon.io/latest/webserver-setup/#nginx

注意点

- nginxとphp-fpmが別々の場合、どちらにも同じソースコードをマウントが必要

________________________________________
### 1.3. phalcon devtools, stub

※ コマンドはlinuxのphp cli、composer、unzipが利用できる環境下で行う

```bash
cd /path/to/project
# 安定版ではなく開発版…
composer require --dev phalcon/devtools:5.0.x-dev
composer require --dev phalcon/ide-stubs:5.6.1

# 動作確認
alias phalcon='./vendor/bin/phalcon'
phalcon
```

スケルトンプロジェクト

```bash
phalcon create-project foo-app

# 補足
publicフォルダがドキュメントルートに来るようにする必要がある
そのため、dockerなどでのマウント位置の調整が必要
```

DBテーブルを基にしたCRUD Scaffolding

```bash
phalcon scaffold --table-name zzz_sample
```

________________________________________
## 2. 構造と仕組み
________________________________________
### 2.1. 概要と雛形の構造

概要

```text
- 典型的なMVCフレームワーク
- DI、Service、Controller、Model、それぞれに大量のマジックプロパティ/メソッドがある
```

ディレクトリ構造

```text
- app
    - config
        - config.php   <--- db接続やphalcon自体の設定。アプリ全体の定数的な値
        - loader.php   <--- オートローダー設定。オートロードするフォルダの追加
        - router.php   <--- ルーティング設定
        - services.php <--- diコンテナ設定
    - controllers
    - models
    - views
    - app.php
- public               <--- document rootにする
    - index.php        <--- エントリポイント

※ 他にもcache、/app/library、/app/migrationsがあるが、重要ではない
※ base dir直下の.htaccess、.htrouter.php、index.htmlはデバッグや設定ミスを表示する目的。本来は不要
```

Webサーバ側のrewrite設定の要約

```text
pathはクエリストリングとして_url=pathになって、index.phpに行くようにする
```

________________________________________
### 2.2. MVCの外側

初期化のあらすじ

```text
1. DIコンテナの生成
2. /config/services.php を実施し、それぞれ登録
    - 'config'         : /config/config.php
    - 'view'           : Phalconのviewsと仲介の仕組み (※1)
    - 'url'            : Phalconのurl生成ヘルパ
    - 'db'             : Phalconのdbヘルパ
    - 'modelsMetadata' : 略 (※2)
    - 'flash'          : Phalconのviewパーツ (※2)
    - 'session'        : Phalconのsessionヘルパ (※2)
3. /config/router.php を実施 (※2)
4. /config/loader.php を実施 (※3)
5. new Phalcon\Mvc\Application($di) (※1)
6. handle($_SERVER['REQUEST_URI'])->getContent() (※1)

(※1) microテンプレートでは仕組みが簡易なものになる
(※2) microテンプレートでは省略
(※3) microテンプレートではルーティングやcontrollerの記述が省略
```

例外の扱い


```text
- index.phpのcatch句で制御すればよい
- https://docs.phalcon.io/latest/application/#catching-exceptions
```

________________________________________
### 2.3. DI、MVC、DB

DIコンテナ

```text
コンテナやサービスのアクセス方法

- Phalcon\DI\Di::getDefault()
    - https://docs.phalcon.io/latest/di/#static-access
- controller's $this->fooService
    - https://docs.phalcon.io/latest/controllers/#dependency-injection
- model's $this->getDI()
    - https://docs.phalcon.io/latest/di/#automatic-injecting

コンテナからサービスの取得

- $container->get('request')
- $container->getRequest()
- $container['request']
    - https://docs.phalcon.io/latest/di/#resolving-services
```

Controller、ルーティング、request、response

```text
継承構造

- Phalcon\Di\Injectable
    - Phalcon\Mvc\Controller

デフォルトルーティング

- Class名Controllerサフィックスが必須。サフィックスを除いてCamelCaseをsnake_caseに変換したurlとマッチ
- method名はActionサフィックス必須。サフィックスを除いてlowerしたものとマッチ
- 3つ目以降のサブパスは引数で取得可能。型チェックもされる

その他（DI関連はDIの項目を参照の事）

- インジェクションされているrequest・responseサービスで入出力の制御を行うと簡単
- Responseインスタンスをreturnすれば、それを基にHttpResponseになる
- Responseインスタンスをreturnしない場合、対応するviewでの出力になる
```

Model

```text
継承構造

- Phalcon\Di\AbstractInjectionAware
    - Phalcon\Mvc\Model

マジックメソッド（DI関連はDIの項目を参照の事）

::findFirstByxxx(value)    : ::findFirst([ "conditions": "[xxx] = ?0", "bind": [value])
->findFirstByxxx(value)    : ::findFirst([ "conditions": "[xxx] = ?0", "bind": [value])
::findByxxx(value)         : ::find([ "conditions": "[xxx] = ?0", "bind": [value])
->findByxxx(value)         : ::find([ "conditions": "[xxx] = ?0", "bind": [value])
::countBy                  : 略
->countBy                  : 略
->xxx                      : ->getXxx()
->xxx                      : ->getRelated('xxx')

仕組み

1. Active Recordである
    - ::find()
    - ::findBy*() (マジックメソッド)
        - これらはSimpleを返す
    - ::findFirst()
    - ::findFirstBy*() (マジックメソッド)
    - $this->save()
    - $this->create()
    - $this->update()
2. テーブル、外部キー
    - $this->setSource()
    - $this->belongsTo()
    - $this->hasMany()
    - $this->hasManyToMany()
    - $this->* (マジックプロパティ)
    - $this->getRelated()
    - $this->getReadConnection()
    - $this->getWriteConnection()

重要な注意点

- Tableに存在しないプロパティをjsonに含めるにはjsonSerializeのoverrideが必要
- MySQLのbit型など、あまり一般的でないDB型は未対応。数値型、文字列型、timestampなどのみを推奨
```

dbヘルパ

```text
R、CUD

- $db->query($sql, $param)
    - $result->fetchAll()
- $db->execute($sql, $param)
    - $db->affectedRows()
    - https://docs.phalcon.io/latest/db-layer/#binding
        - ドキュメントが間違いで、実際はちゃんとprepareしてくれる
        - https://github.com/phalcon/cphalcon/blob/14b0f1cb8ac9240c06c2c0768339f233177dddfb/phalcon/Db/Adapter/Pdo/AbstractPdo.zep#L715
        - https://github.com/phalcon/cphalcon/blob/14b0f1cb8ac9240c06c2c0768339f233177dddfb/phalcon/Db/Adapter/Pdo/AbstractPdo.zep#L426

トランザクション

- $db->begin()
- $db->isUnderTransaction()
- $db->rollback()
- $db->commit()
    - https://docs.phalcon.io/latest/db-layer/#transactions

SQLロギング

    - https://docs.phalcon.io/latest/db-layer/#logging
```

Model vs DBヘルパ

```text
- どちらからでもDBとのやり取りが可能
- 簡単な記述なら混在も可能（単純なトランザクション制御など）
```

________________________________________
## 3. PHPUnitと統合
________________________________________
手順

1. composer require --dev phpunit/phpunit
2. tests/phalcon_test_helper.php
    - public/index.phpを参考に、autoloadに必要な記述を記載
3. tests/SampleTest.php
4. phpunit.xml
    - 最低限bootstrapとdirectoryだけ記載があればOK
5. コンテナ内からvendor/bin/phpunitでテストが実行されることを確認
6. 必要に応じてvscodeのPHPUnit Test Explorer拡張を追加し、settings.jsonを調整して実行できるように