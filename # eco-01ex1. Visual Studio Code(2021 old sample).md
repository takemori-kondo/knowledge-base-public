# eco-01ex1. Visual Studio Code(2021 old sample)
________________________________________
## 1. Environment
________________________________________
### 1.1. Version

- Windows10 Pro 1809 17763.973
- MeiryoKe_Console 6.30rev5
- Visual Studio Code 1.42.1

________________________________________
### 1.2. Tips

Exclude Windows Defender

```Text
Settings
-> Update & Security
-> Windows Security
-> Virus & threat protection
-> Virus & threat protection settings
-> Exclusions
```

VSCode Find Exclusions

```text
**/vendor,**/node_modules
```

________________________________________
## 2. Extensions & External Tools
________________________________________
### 2.1. Extensions Summary

Formatter/Intellisenser

```text
languages  : default / extensions
----------------------------------------
html/js    : poor    / Beautify(*1), ESLint(*1)
json       : yes     / Beautify
php(*2)    : no      / PHP Intellisense(*1), phpcs(*1), php-cs-fixer(*1)
ruby       : no      / -
xml        : no      / XML Tools
```

Debugger

```text
languages  : default / extensions
----------------------------------------
html/js    : no      / Debugger for Chrome
json       : no      / -
php        : no      / PHP Debug
ruby       : no      / Ruby & Ruby Solargraph
xml        : no      / -
```

Others

```text
- Whitespace+(*1)
```

(*1) Please read next section.

(*2) PHP関連の拡張について

```text
PHP Intellisense           : サジェスタ・検索強化
PHP CodeSniffer            : linter
PHP Coding Standards Fixer : フォーマッタ
2022年では、PHP Intelephenseが上記3つの役割をこなしてフォーマットにPHP本体も不要なので楽
PHP Intelephenseの場合、以下の2つは設定して他はデフォルトでPSR12を前提とするイイ感じになる
    "intelephense.environment.phpVersion": "8.1.0",
    "intelephense.environment.shortOpenTag": false,
```

________________________________________
### 2.2. Set up php.exe

1. Get php.exe set
2. Put to C:/php/<version-folder>/php.exe
3. Add path to env PATH
4. Copy to php.ini form php.ini-development
5. Uncomment it

```ini
extension_dir = "ext"
...
extension=intl
extension=imap
...
extension=mbstring
...
extension=openssl
...
extension=pdo_mysql
...
extension=pdo_pgsql
```

________________________________________
### 2.3. Install list

External tools

- npm
- eslint
- php.exe
- composer
- phpcs
- php-cs-fixer

Extensions

- Beautify (settings.json)
- PHP Intellisense
- phpcs
- php cs fixer

Whitespace+

```shell
# 1. Show config.json in "Whitespace+" extension (Run command palette)
#     Whitespace+ Config
# 2. Set "pattern" value for "space"
#     "pattern": "\\s{2,}",
# 3. Reboot vscode
# 4. Set the following in keybindings.json
#     // https://github.com/davidhouchin/whitespace-plus/blob/master/package.json
#     { "key": "ctrl+shift+tab",
#       "command": "extension.toggleWhitespacePlus",
#       "when": "editorTextFocus"
#     }
```

________________________________________
### 2.4. Extension Settings

settings.json

```json
{
    // common settings
    "files.eol": "\n",
    "editor.detectIndentation": false,
    "editor.insertSpaces": true,
    "editor.renderIndentGuides": true,
    "editor.renderWhitespace": "boundary",
    "editor.tabSize": 4,
    "window.zoomLevel": 0,
    "editor.fontFamily": "MeiryoKe_Console, monospace",
    "git.ignoreMissingGitWarning": true,
    "terminal.integrated.fontFamily": "MeiryoKe_Console, monospace",
    "terminal.integrated.shell.windows": "C:\\WINDOWS\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
    "terminal.integrated.env.windows": {
        "PSExecutionPolicyPreference": "RemoteSigned"
    },

    // Beautify
    "html.format.unformatted": null,
    "[html]": {
        "editor.defaultFormatter": "HookyQR.beautify"
    },
    "[javascript]": {
        "editor.defaultFormatter": "HookyQR.beautify"
    },

    // Debugger for Chrome
    // -

    // ESLint
    // -
    
    // PHP Debug
    // -

    // PHP IntelliSense
    "php.suggest.basic": false,
    "php.validate.executablePath": "C:/php/php-7.2.9-Win32-VC15-x64/php.exe",
    "php.executablePath": "C:/php/php-7.2.9-Win32-VC15-x64/php.exe",
    
    // PHP CodeSniffer
    "phpcs.standard": "PSR2",
    
    // PHP Coding Standards Fixer
    "php-cs-fixer.executablePath": "C:/Users/<user>/AppData/Roaming/Composer/vendor/bin/php-cs-fixer",
    "php-cs-fixer.executablePathWindows": "C:/Users/<user>/AppData/Roaming/Composer/vendor/bin/php-cs-fixer.bat",
    "php-cs-fixer.formatHtml": true,
    
    // Ruby & Ruby Solargraph
    // -
    
    // Whitespace+
    // Run "Whitespace+ Config" on command pallete(ctrl + shift + c)
    // XML Tools
    // -
}
```

config.json for Whitespace+

```json
...
        "name": "space",
        "enabled": true,
        "pattern": "\\s{2,}",
...
```
