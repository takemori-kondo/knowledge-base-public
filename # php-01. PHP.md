# php-01. PHP
________________________________________
## 1. Terms & Environment
________________________________________
### 1.1. Create LAMP

If use xampp, read "1.1.ex. Xampp"

If use centos, read "middleware-01. LAMP"(other page)

________________________________________
### 1.1.ex. Xampp

1. Disable IIS
    1. Control Panel
    2. Programs and Features
    3. Turn Windows features on or off
    4. Internet Information Service
2. Download & unzip Xampp
    1. e.g. https://sourceforge.net/projects/xampp/files/XAMPP%20Windows/7.2.3/
3. Put it on C:\
4. Download and setup php X Debug TS 32bit
    1. e.g. https://xdebug.org/files/php_xdebug-2.6.0-7.2-vc15.dll
    2. Put it on <xampproot>\php\ext
    3. Add settings to <xampproot>\php\php.ini
5. Run xampp-control.exe, and start Apache & MySQL

```ini
[XDebug]
zend_extension = "C:\xampp\php\ext\php_xdebug-2.6.0-7.2-vc15.dll"
xdebug.profiler_append=0
xdebug.profiler_enable=0
xdebug.profiler_enable_trigger = 0
xdebug.profiler_output_dir="c:\xampp\tmp"
xdebug.remote_autostart = 1
xdebug.remote_cookie_expire_time = 36000
xdebug.remote_enable=1
xdebug.remote_handler="dbgp"
xdebug.remote_host="localhost"
xdebug.remote_port=9000
```

Confirm versions by shell of xampp

```shell
httpd -v
mysql -v
php -v
```

Supplement

1. Web server's document-root is htdocs.
2. Web site url is localhost.

________________________________________
### 1.2. Install Environment

Install

```text
1. Chrome (by installer)
2. Visual Studio Code (by installer)
3. PHP Debug extension
4. php.exe (https://windows.php.net/download)
  4.1. Composer (by installer)
    4.1.1. phpcs (by composer) & phpcs extension
    4.1.2. phpcs fixer (by composer) & phpcs fixer extension (junstyle)
  4.2. PHP IntelliSense extension
```

phpcs, phpcs-fixer via composer

```shell
composer global require squizlabs/php_codesniffer=*
composer global require friendsofphp/php-cs-fixer=*
```

________________________________________
### 1.3. VSCode Workspace Settings

settings.json（workspace）

```json
{
    // common settings
    "editor.detectIndentation": false,
    "editor.insertSpaces": true,
    "editor.renderIndentGuides": true,
    "editor.renderWhitespace": "boundary",
    "editor.tabSize": 4,
    
    ...
    
    // PHP Debug
    // -
    // PHP IntelliSense
    "php.suggest.basic": false,
    "php.validate.executablePath": "C:/php/php-7.2.3-Win32-VC15-x64/php.exe",
    "php.executablePath": "C:/php/php-7.2.3-Win32-VC15-x64/php.exe",
    // PHP CodeSniffer
    "phpcs.standard": "PSR2",
    // PHP Coding Standards Fixer
    "php-cs-fixer.executablePath": "C:/Users/<user>/AppData/Roaming/Composer/vendor/bin/php-cs-fixer",
    "php-cs-fixer.executablePathWindows": "C:/Users/<user>/AppData/Roaming/Composer/vendor/bin/php-cs-fixer.bat",
    "php-cs-fixer.formatHtml": true,
    
    ...
```

________________________________________
### 1.4. VSCode Debug Settings

1. 市販ウイルス対策ソフト、Windows Defenderなどのファイアーウォールのポート9000を開ける
2. Visual Studio Codeで開く
3. デバッグ実行
4. どの環境で初期化するか聞かれる
    - 拡張で入れたPHPを選択
    - .vscode/launch.jsonが自動生成
5. launch.json書き換え

```json
{
    // IntelliSense を使用して利用可能な属性を学べます。
    // 既存の属性の説明をホバーして表示します。
    // 詳細情報は次を確認してください: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for XDebug",
            "type": "php",
            "request": "launch",
            "port": 9000,
            "pathMappings": {
                "/var/www/html": "${workspaceRoot}"
            }
        }
    ]
}
```

________________________________________
## 2. PHP
________________________________________


________________________________________
### 2.3. Class

php01/MyClass.php

```php
<?php

namespace Php01;

/**
 * Simple base class.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */
class MyClass
{
    /**
     * Class(static) property
     *
     * @var string $staticProperty
     */
    public static $staticProperty = "staticProperty's value";

    /**
     * Class(static) method
     *
     * @return void
     */
    public static function staticMethod()
    {
        echo 'staticMethod() called'.'<br>';
    }

    /**
     * Constructor (= new)
     *
     * @param string $p1 This sets to $instanceProperty
     */
    public function __construct($p1)
    {
        echo '<br>'.'__construct($p1) called. $p1 is '.$p1.'<br>';
        $this->instanceProperty = $p1;
    }

    // Instance property
    protected $instanceProperty;

    /**
     *  Instance method
     *
     * @return void
     */
    public function instanceMethod()
    {
        echo 'instanceMethod() called.'.'<br>';
        echo '"instanseProperty"='.$this->instanceProperty.'<br>';
    }
}
```

php01/MyClassMk2.php

```php
<?php

namespace Php01;

/**
 * Simple extended class.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */
class MyClassMk2 extends MyClass
{
    /**
     *  Instance method
     *
     * @return void
     */
    public function instanceMethod()
    {
        echo 'Overrided method!'.'<br>';
        echo '"instanseProperty"='.$this->instanceProperty.'<br>';
    }
}
```

php01/syntax02-class.php

```php
<?php
/**
 * Class definition sample.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */
namespace Php01;

require_once __DIR__.'/MyClass.php';
require_once __DIR__.'/MyClassMk2.php';

var_dump(MyClass::$staticProperty);
MyClass::staticMethod();
$a = new MyClass('Foo');
$a->instanceMethod();
$b = new MyClass('Bar');
$b->instanceMethod();
$c = new MyClassMk2('Baz');
$c->instanceMethod();

/*
PHP Specific Features

PHPのオブジェクト指向とstaticの関係は他の言語と異なる。
フィールドはstatic・インスタンスに分かれ他の言語と同様であるが、メソッドはインスタンス・staticメソッドには分かれない。
<Class名>::、self::、static::、parent::いずれもいわゆる他の言語で言うところのインスタンスメソッドがコール可能である。
オブジェクトメソッドか、staticに呼ばれるかは、呼び出し元の文脈で決まり、$thisも文脈に従う。

use        require_onceはファイル読み込み、useは名前空間の解決(他の用途でtraitの利用)
self::     記述位置のクラスのメソッドを呼ぶ
static::   メソッドの場合は、呼び出し元の文脈に従う
parent::   記述クラスの親クラスのメソッドを呼ぶ
::class    クラスの完全名を文字列で取得
overload   PHPのオーバーロードは、未宣言プロパティへの動的確保set/get機能
__get      マジックメソッド。overloadの挙動を決める。未定義時は未宣言プロパティへset済みなら取得できる
__set      マジックメソッド。overloadの挙動を決める。未定義時は未宣言プロパティへsetできる
__invoke   マジックメソッド。インスタンス自体を()することで呼び出される関数
trait      Mix-in
Anonymous  無名クラス
foreach    インスタンスに対してforeach可能
==         同じ型であり、全てのプロパティが==比較でTrueかを調べる
*/
```

________________________________________
### 2.4. PDO

php01/DBMysql.php

```php
<?php

namespace Php01;

/**
 * PDO Provider for MySQL.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */
class DBMysql
{
    /**
     * Inner variable.
     *
     * @var \PDO $pdo
     */
    protected static $pdo;

    /**
     * Singleton getter.
     *
     * @return \PDO
     */
    public static function getPdo()
    {
        if (static::$pdo == null) {
            static::$pdo = static::newPdo();
        }
        return static::$pdo;
    }

    /**
     * Create pdo instance.
     *
     * @return \PDO;
     */
    protected static function newPdo()
    {
        try {
            $dbServer = 'mysql';
            $dbHost = '127.0.0.1';
            $dbCharset = 'utf8mb4';
            $dsn = $dbServer.':host='.$dbHost.';charset='.$dbCharset;
            $dbUser = 'root';
            $dbPass = 'foo';
            $driverOption = array(\PDO::ATTR_EMULATE_PREPARES => false);

            $created = new \PDO($dsn, $dbUser, $dbPass, $driverOption);
            $created->setAttribute(\PDO::MYSQL_ATTR_USE_BUFFERED_QUERY, true);
            return $created;
        } catch (\Exception $e) {
        }
    }
}
```

php01/use-pdo.php

```php
<?php
/**
 * PDO sample.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */
namespace Php01;

require_once __DIR__.'/DBMysql.php';

// Get pdo instance.
$pdo = DBMysql::getPdo();
 
// If DB does not exist, creating it.
$dbName = 'php_sample';
if ($pdo->query("SHOW DATABASES LIKE '$dbName'")->rowCount() <= 0) {
    $pdo->exec("CREATE DATABASE ".$dbName);
}
$pdo->exec("USE ".$dbName);

// If table does not exist, creating it.
$tableName = 'sample_values';
if ($pdo->query("SHOW TABLES LIKE '$tableName'")->rowCount() <= 0) {
    $sql = "CREATE TABLE ".$tableName."(id BIGINT AUTO_INCREMENT, name VARCHAR(255), PRIMARY KEY(id))";
    $pdo->exec($sql);
    $names = ['Alice', 'Bob', 'Carol', 'Dave'];
    $stmt = $pdo->prepare("INSERT INTO ".$tableName."(name) values(:name)");
    foreach ($names as $name) {
        $stmt->bindParam(':name', $name, \PDO::PARAM_STR);
        $stmt->execute();
    }
}

// Select table and output
$tableName = 'sample_values';
$stmt = $pdo->query("SELECT * FROM ".$tableName." ORDER BY id");
foreach ($stmt as $value) {
    var_dump($value);
}
```
