# eco-05. Package Manager
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

Prepare composer

```bash
# 1. Install php-cli

# 2. Download & install composer
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
```

Restore packages for existing project

```bash
composer install
```

for New project (skeleton framework)

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