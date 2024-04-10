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

Formatter/Intellisenser

```text
languages  : default / extensions
----------------------------------------
html/js/ts : poor(*1)/ ESLint
angular    : no      / Angular Language Service
json       : yes     / -
php        : no      / PHP Intelephense
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
- Whitespace+
- Rainbow Csv
```

(*1) Beautifyがなくてもそれなりに整形されるようになった

________________________________________
### 2.2. 典型的な基本設定

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
    
    ...
}
```

________________________________________
### 2.3. Whitespace+の設定

典型的な設定

1. "Whitespace+" を追加 
2. コマンドパレットで以下を実行してConfigを開く
    - Whitespace+ Config
3. "space"のパターンを設定
    - "pattern": "\\s{2,}",
4. vscodeを再起動
5. vsのショートカットに表示切替を追加     

config.json for Whitespace+

```json
...
        "name": "space",
        "enabled": true,
        "pattern": "\\s{2,}",
...
```

keybindings.json

```
// 既定値を上書きするには、このファイル内にキー バインドを挿入します
[
  {
    // https://github.com/davidhouchin/whitespace-plus/blob/master/package.json
    "key": "ctrl+shift+tab",
    "command": "extension.toggleWhitespacePlus",
    "when": "editorTextFocus"
  }
]
```