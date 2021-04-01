# php-02. Todo List
________________________________________
## 1. Todo List
________________________________________
php02/add.php

```php
<?php
/**
 * TODO list sample's adder.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */
namespace Php02;

require_once __DIR__.'/DBMysql.php';
require_once __DIR__.'/DataSource.php';

$pdo = DataSource::openOrInitializeDB();

$todos = $_POST['todos'];
$stmt = DataSource::updateAllTodos($pdo, $todos);
$stmt = DataSource::addTodo($pdo);
header("Location: ./index.php");
```

php02/DataSource.php

```php
<?php

namespace Php02;

/**
 * Todo data source.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */
class DataSource
{
    /**
     * Connect DB.
     *
     * @param string $dbName DB name.
     *
     * @return \PDO
     */
    public static function openOrInitializeDB($dbName = 'todo_list')
    {
        // Connect DB server.
        $pdo = DBMysql::getPdo();

        // Open or create DB.
        if ($pdo->query("SHOW DATABASES LIKE '$dbName'")->rowCount() <= 0) {
            $pdo->exec("CREATE DATABASE ".$dbName);
        }
        $pdo->exec("USE ".$dbName);

        // Open or create table.
        $tableName = 'todos';
        if ($pdo->query("SHOW TABLES LIKE '$tableName'")->rowCount() <= 0) {
            // This sql requires mysql5.6/mariadb10.1 or more.
            $sql1 = "CREATE TABLE ".$tableName
                ."( id       INT AUTO_INCREMENT"
                .", name     VARCHAR(255)"
                .", content  VARCHAR(255)"
                .", created  DATETIME DEFAULT CURRENT_TIMESTAMP"
                .", modified DATETIME DEFAULT CURRENT_TIMESTAMP"
                ."                    ON UPDATE CURRENT_TIMESTAMP"
                .", PRIMARY KEY(id))";
            $pdo->exec($sql1);

            $sql2 = "INSERT INTO ".$tableName
                ."       ( name,  content)"
                ." values(:name, :content)";
            $stmt = $pdo->prepare($sql2);
            $data = (
                ['TODOサンプル1' => '〇〇を××する'
                ,'TODOサンプル2' => '〇〇を△△する'
                ,'TODOサンプル3' => '〇〇を□□する']
            );
            foreach ($data as $name => $content) {
                $stmt->bindParam(':name', $name, \PDO::PARAM_STR);
                $stmt->bindParam(':content', $content, \PDO::PARAM_STR);
                $stmt->execute();
            }
        }
        return $pdo;
    }

    /**
     * Get all todos.
     *
     * @param \PDO $pdo pdo instance.
     *
     * @return \PDOStatement
     */
    public static function getAllTodos($pdo)
    {
        return $pdo->query("SELECT * FROM todos ORDER BY id");
    }

    /**
     * Add todo.
     *
     * @param \PDO $pdo pdo instance.
     *
     * @return \PDOStatement
     */
    public static function addTodo($pdo)
    {
        return $pdo->query("INSERT INTO todos(name, content) values('新しいTODO', '内容を入力してください')");
    }

    /**
     * Update all todos.
     *
     * @param \PDO  $pdo   pdo instance.
     * @param array $todos ["<id>" => ["name" => "<name>", "content" => "<content>"]];.
     *
     * @return \PDOStatement
     */
    public static function updateAllTodos($pdo, $todos)
    {
        $stmt = $pdo->prepare("UPDATE todos SET name=:name, content=:content WHERE id=:id");
        foreach ($todos as $id => $assoc) {
            $stmt->bindParam(':id', $id, \PDO::PARAM_INT);
            $stmt->bindParam(':name', $assoc['name'], \PDO::PARAM_STR);
            $stmt->bindParam(':content', $assoc['content'], \PDO::PARAM_STR);
            $stmt->execute();
        }
        return $stmt;
    }

    /**
     * Delete todo.
     *
     * @param \PDO   $pdo pdo instance.
     * @param string $id  id.
     *
     * @return \PDOStatement
     */
    public static function deleteTodo($pdo, $id)
    {
        $stmt = $pdo->prepare("DELETE FROM todos WHERE id=:id");
        $stmt->bindParam(':id', $id, \PDO::PARAM_STR);
        $stmt->execute();
        return $stmt;
    }
}
```

php02/DBMysql.php

```php
<?php

namespace Php02;

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

php02/delete.php

```php
<?php
/**
 * TODO list sample's deleter.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */
namespace Php02;

require_once __DIR__.'/DBMysql.php';
require_once __DIR__.'/DataSource.php';

$pdo = DataSource::openOrInitializeDB();
if (isset($_POST['target-id']) && !empty($_POST['target-id'])) {
    $stmt = DataSource::deleteTodo($pdo, $_POST['target-id']);
}
header("Location: ./index.php");
```

php02/functions.php

```php
<?php
/**
 * TODO list sample's functions.php.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */
namespace Php02;

function strUtcToStrTokyo($strUtcDate)
{
    $oDate = new \DateTime($strUtcDate, new \DateTimeZone('UTC'));
    $oDate->setTimezone(new \DateTimeZone('Asia/Tokyo'));
    return $oDate->format('Y-m-d H:i:s');
}
```

php02/index.php

```php
<?php
/**
 * TODO list sample's index.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */
namespace Php02;

require_once __DIR__.'/DBMysql.php';
require_once __DIR__.'/DataSource.php';
require_once __DIR__.'/functions.php';

// Get pdo instance.
$pdo = DataSource::openOrInitializeDB();
$stmt = DataSource::getAllTodos($pdo);
?>
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8" />
    <title>HTML Samples</title>
</head>

<body>
    <div id="wrap">
        <header>
            <div>TODOリスト</div>
        </header>
        <main>
            <form id="form" method="POST" action="./update.php">
                <input type="hidden" id="target-id" name="target-id" value="">
                <?php $iTab = 1; ?>
                <?php foreach ($stmt as $key => $value) : ?>
                <div>
                    <input name="todos[<?php echo $value['id']; ?>][name]" value="<?php echo $value['name']; ?>"
                        tabindex="<?php echo $iTab++; ?>" type="text">
                    <input name="todos[<?php echo $value['id']; ?>][content]" value="<?php echo $value['content']; ?>"
                        tabindex="<?php echo $iTab++; ?>" type="text">
                    <button name="delete" onclick="submitDataTo('./delete.php', '<?php echo $value['id']; ?>')"
                        tabindex="<?php echo $iTab++; ?>" type="button">削除</button>
                    作成日:
                    <?php
                    $strUtcDate = $value['created'];
                    echo strUtcToStrTokyo($strUtcDate); ?>
                    更新日:
                    <?php
                    $strUtcDate = $value['modified'];
                    echo strUtcToStrTokyo($strUtcDate); ?>
                </div>
                <?php endforeach ?>
                <div>
                    <button name="add" onclick="submitDataTo('./add.php')" tabindex="<?php echo $iTab++; ?>"
                        type="button">保存&amp;追加</button>
                    <button name="add" onclick="submitDataTo('./updateAll.php')" tabindex="<?php echo $iTab++; ?>"
                        type="button">保存</button>
                </div>
            </form>
        </main>
        <footer>
            <div>Copyright © 2018 &lt;your name&gt; All Rights Reserved. </div>
        </footer>
    </div>
    <script>
        function submitDataTo(formAction, opt_id) {
            document
                .getElementById('form')
                .setAttribute('action', formAction);
            document
                .getElementById('target-id')
                .setAttribute('value', opt_id);
            document
                .getElementById('form')
                .submit();
        }
    </script>
</body>

</html>
```

php02/updateAll.php

```php
<?php
/**
 * TODO list sample's updater.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */
namespace Php02;

require_once __DIR__.'/DBMysql.php';
require_once __DIR__.'/DataSource.php';

$pdo = DataSource::openOrInitializeDB();

$todos = $_POST['todos'];
$stmt = DataSource::updateAllTodos($pdo, $todos);
header("Location: ./index.php");
```
