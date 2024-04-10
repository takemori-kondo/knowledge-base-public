# eco-05. Package Manager
________________________________________
## 0. 要約
________________________________________

※ この表は挙動の確認まで正確に行っていないので、間違いがある可能性あり。2022/05時点

操作                                   |npm               |composer                     |bundler                  |yum
---------------------------------------|------------------|-----------------------------|-------------------------|--------------------------
globalの思想                           |グローバル        |グローバル                   |全バージョン保持         |undo不可な可能性あり
依存宣言ファイル                       |package.json      |composer.json                |Gemfile                  |-
global listup                          |npm list -g       |(composer global list)       |-                        |yum list
global restore by lock                 |-                 |-                            |bundle install           |-
global add                             |npm install -g XXX|(composer global require XXX)|bundle install XXX       |yum -y install XXX
recreate lock                          |npm install       |composer update              |bundle update            |-
replace newer version                  |npm update        |composer update              |-                        |yum -y update XXX
依存宣言ファイルの用意                 |npm init -y       |composer init                |bundle init              |-
localまたは依存宣言ファイル内の一覧    |npm list          |composer list                |bundle list              |-
local restore by lock                  |npm ci            |composer install             |-                        |-
local add                              |npm install XXX   |composer require XXX         |-                        |-
localまたは依存宣言ファイルベースの実行|npx XXX           |?                            |bundle exec XXX          |-

```text
※ npmの最大のひねくれポイントは、他の製品の慣習に逆らってnpm installがlockファイルを積極的に変更することである
※ npm系のパッケージは、npm自体や、Node.js本体に依存している場合が多い
   特に、依存関係がNode.jsのメジャーバージョンレベルで異なる場合、複数Node.js運用を可能にするnvmまたはnvm-windowsが必須になる
※ RubyGems単品(gemコマンド)は、依存関係を考慮する機能がなかったため依存関係をサポートするbundlerが作られた、という歴史的経緯がある
   後発のnpmやcomposerはbundlerの影響を強く受けている
※ なお、アプリケーションライブラリレベルのパッケージマネージャはおそらくPerlのCPAN、JavaのMaven辺りが最古参であり、まだgitはおろかsvnが出たてのころである
   もっと言えば、MavenにしてもGradleにしてもビルドツールの側面が前面に出ていて、パッケージマネージャである、という表現は足りていない
※ bundlerは、他のパッケージマネージャと異なり、全てのライブラリの全てのバージョンを保持する
   そのため、updateは実際には新しいバージョンをダウンロードして、GemFileやlockファイルを書き換えているのみで、旧バージョンの削除は行わない
   この設計思想の違いから、プロジェクトローカルにinstallする必然性がなく、グローバルにinstallすればよい
※ railsコマンドだけは、bundlerに頼らずともバージョン指定が可能な仕組みが内蔵されている
```

________________________________________
## 1. Application Package Manager
________________________________________ 
### 1.1. CentOS (yum & rpm)

Prepare yum & rpm

```bash
# Installed by default
```

Example of using yum & rpm

```bash
yum -y install epel-release
rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
yum list --enablerepo=remi-php70 | grep php70
yum -y install --enablerepo=remi-php70 php
```

________________________________________
## 2. Library Package Manager
________________________________________
### 2.1. Ruby (gem & bundler)

Prepare gem & bundler

```bash
# 1. Install ruby by ruby installer.
#    Gem is installed by ruby installer.

# 2. Install bundler
gem list
gem install bundler

# 3. Install rails
gem install rails
```

Restore packages for existing project

```bash
bundle install
```

for New project (skeleton framework)

```bash
rails new your-project-name
```

for New project (empty)

```bash
# Create blank Gemfile(package list) for workspace.
bundle init

# Setting local install mode.
bundle install --path vendor/bundle
```

________________________________________
### 2.2. PHP (composer)

※ 前提として、開発環境でも本番環境と同等のOS上から行うこと

Prepare composer

```bash
# 1. Install php-cli

# 2. Download & install composer.phar
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"

# 3. 動作確認
composer --version
```

Restore packages for existing project

```bash
composer install
```

Add sample (e.g. phpmailer)

```bash
composer require phpmailer/phpmailer 
```

for New framework project (skeleton framework)

```bash
# f.g. cakephp skeleton
composer create-project cakephp/app your-project-name --prefer-dist

# f.g. laravel skeleton
composer create-project laravel/laravel your-project-name --prefer-dist
```

for New project (empty)

```bash
composer init
```

Other samples

```bash
composer search phpoffice/phpexcel
composer require phpoffice/phpexcel
composer install
```

________________________________________
### 2.3. JS/CSS (npm)

Prepare npm

```text
# Install by Node.js installer.

# インストールが完了すると、以下のPATHも設定されます
## C:\Users\<ユーザ>\AppData\Roaming\npm
## C:\Program Files\nodejs\
```

for New project (empty)

```bat
npm init -y
```

Other samples

```bat
npm install jquery --save
npm install block-ui --save

# ./node_modules/.bin/ とほぼ等価。powershellの場合、ExecutionPolicyしてくれる
npx --no-install eslint .\js\javascript-hello-world.js
```

________________________________________
### 2.4. .NET (NuGet)

Prepare nuget

```text
# Nuget is installed by visual studio 20XX installer.
```

How to use nuget

```text
Visual studio menu
```

Other samples

```bat
# NuGet sample.
Get-Package -Filter jQuery.UI -ListAvailable
Get-Package -Updates
Install-Package jQuery
Update-Package
Uninstall-Package jQuery
```

________________________________________
### 2.5. xCode (CocoaPods)

Prepare CocoaPOds

```text
sudo gem install cocoapods
```

Restore packages for existing project

```text
pod install
```