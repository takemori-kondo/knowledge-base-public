# front-07. Angular.js
________________________________________
## 1. Prepare
________________________________________
Install Git

http://git-scm.com/download/win

Download by npm

```shell
# nodeとバージョンが食い違うと動かないので、本当はバージョン指定を推奨
npm install -g @angular/cli
```

Initialize project folder

```shell
git --version
ng new front07
```

Transpile & Run by localhost:4200

```shell
cd front07
ng serve

# ** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **
# と出れば、成功。上記URLでブラウザからアクセスできる
```