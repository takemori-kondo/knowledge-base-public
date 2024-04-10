# php-01. PHP
________________________________________
## 1. Terms & Environment
________________________________________
### 1.1. Create LAMP

If use xampp, read "php-01ex. Windows Xampp"(other page)

If use ubuntu, read "vm-03ex. Ansible from centos to ubuntu"(other page)

※ SQLiteのインストールがAnsibleスクリプトに組み込まれていないので、必要に応じてインストールして下さい

```bash
# ubuntuの例。webサーバの再起動も必要
sudo apt install -y php8.1-sqlite3
```

※ ComposerのインストールがAnsibleスクリプトに組み込まれていないため、必要に応じてubuntu配下でインストールして下さい

※ 本番がLinuxの場合はローカル環境もLinuxにし、Composer操作等も全てLinux上で行いましょう

※ eco-05. Package Manager の Composerも参考に初期設定して下さい

```bash
# 例：PHPMailerをComposer経由で追加
cd /var/www/html/PhpSamples/Php03
composer require phpmailer/phpmailer 
```

※ サーバ側Xdebug3のインストールがAnsibleスクリプトに組み込まれていないため、必要に応じてubutu配下でインストールして下さい

```bash
# ubuntuの例
sudo apt install -y php8.1-xdebug

# php.ini
sudo vi /etc/php/8.1/mods-available/xdebug.ini

# webサーバの再起動も必要
```

/etc/php/8.1/mods-available/xdebug.ini

```ini
zend_extension=xdebug.so
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_host=192.168.3.2
xdebug.client_port = 9000
```

________________________________________
### 1.2. Install Environment

Install

```text
1. Chrome (by installer)
2. Visual Studio Code (by installer)
3. PHP Debug extension
4. PHP Intelephense extension
```

________________________________________
### 1.3. VSCode Workspace Settings

settings.json（workspace）

```json
{    
    ...
    
    // common settings(php)
    "php.suggest.basic": false,
    "php.validate.enable": false,
    // PHP Debug
    // -
    // PHP Intelephense
    "intelephense.environment.phpVersion": "8.1.0",
    "intelephense.environment.shortOpenTag": false,
    
    ...
}
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
                "/var/www/html/PhpSamples": "${workspaceRoot}"
            }
        }
    ]
}
```