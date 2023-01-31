# front-03. Bootstrap5
________________________________________
## 1. Prepare
________________________________________
Download by npm

```shell
cd front03
npm init -y
npm install bootstrap
npm install @popperjs/core
npm install --save-dev eslint
npm install --save-dev eslint-plugin-html
npx eslint --init
    ? How would you like to use ESLint? (Use arrow keys)
        > To check syntax and find problems
    ? What type of modules does your project use? (Use arrow keys)
        > None of these
    ? Which framework does your project use? (Use arrow keys)
        > None of these
    ? Does your project use TypeScript? (y/N)
        > n
    ? Where does your code run? (Press <space> to select, <a> to toggle all, <i> to invert selection)
        > (*) Browser
    ? What format do you want your config file to be in? (Use arrow keys)
        > JavaScript
```

Summery

```text
1. bootstrapを適用すると、デフォルトのbox-sizingがborder-boxになる
2. カルーセルやナビゲーションなど、一通りそろっている
3. 公式リファレンスが充実しているので、そちらを見ること
4. bootstrap5から、jQueryに依存しなくなった
```

________________________________________
## 2. Samples
________________________________________
Layout

- FrontSamples/front03/bootstrap-content-root.html

など