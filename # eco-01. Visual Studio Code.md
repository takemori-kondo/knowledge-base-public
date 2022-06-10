# eco-01. Visual Studio Code
________________________________________
## 1. Environment
________________________________________
### 1.1. Version

- Windows10 Pro 20H2 19042.1645
- MeiryoKe_Console 6.30rev5
- Visual Studio Code 1.66.2

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

※ 最近のVSCodeはデフォルトのフォーマッタがだいぶ良くなったので、デフォルトでも十分使える

Formatter/Intellisenser

```text
languages  : default / extensions
----------------------------------------
html/js/ts : poor(*3)/ ESLint(*1)
json       : yes     / -
php(*2)    : no      / PHP Intelephense
ruby       : no      / -
xml        : no      / XML Tools
```

Debugger

```text
languages  : default / extensions
----------------------------------------
html/js/ts : yes     / -
json       : -       / -
php        : no      / PHP Debug
ruby       : no      / Ruby & Ruby Solargraph
xml        : -       / -
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

(*3) Beautifyがなくてもそれなりに整形されるようになったため、こだわらなければ不要

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
- php.exe & composer

Extensions

- ESLint
- PHP Debug
- PHP Intelephense
- Whitespace+

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
    "editor.guides.indentation": true,
    "editor.insertSpaces": true,
    "editor.renderWhitespace": "boundary",
    "explorer.confirmDelete": false,
    "window.zoomLevel": 0,
    "editor.fontFamily": "MeiryoKe_Console, monospace",
    "git.ignoreMissingGitWarning": true,
    "terminal.integrated.fontFamily": "MeiryoKe_Console, monospace",
    "terminal.integrated.defaultProfile.windows": "PowerShell",

    // common settings(php)
    "php.suggest.basic": false,
    "php.validate.enable": false,

    // intelephense
    "intelephense.environment.phpVersion": "8.1.0",
    "intelephense.environment.shortOpenTag": false,
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
