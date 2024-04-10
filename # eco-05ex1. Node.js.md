# eco-05ex1. Node.js
________________________________________
## 1. インストール
________________________________________
Windowsの場合（nvm-windows経由）

1. nvm-windowsをインストールする
2. 典型的なインストール

```powershell
nvm root $env:USERPROFILE\AppData\Roaming\nvm
nvm list

# Angular 7~11
nvm install 10

# Angular 12~14
nvm install 14

# Angular 15~
nvm install 18

# 切り替え（node.jsインストール先をProgram Files下にしておいた場合、管理者権限が必要）
nvm use 10
node --version

# 切り替え（node.jsインストール先をProgram Files下にしておいた場合、管理者権限が必要）
nvm use 14
node --version

# 切り替え（node.jsインストール先をProgram Files下にしておいた場合、管理者権限が必要）
nvm use 18
node --version
```