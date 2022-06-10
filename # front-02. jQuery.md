# front-02. jQuery
________________________________________
## 1. Prepare
________________________________________
Initialize & download by npm

```shell
cd front02
npm init -y
npm install jquery --save
npm install block-ui --save
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

Knowledge

```text
temp store for css      : attr(), removeAttr()
temp store for property : prop()   
class                   : addClass(), removeClass(), hasClass(), toggleClass()
value                   : val()
selected via select     : val()
content  via textarea   : val()
checked  as boolean     : prop()
disabled as boolean     : prop()
other attribute         : prop()
```

Toggle

```text
fadeToggle()
slideToggle()
toggle()
```

________________________________________
## 2. Samples
________________________________________
Prop

- FrontSamples/front02/jquery-01-prop-sample.html

Toggle

- FrontSamples/front02/jquery-02-toggle-behavior.html
- FrontSamples/front02/js/jquery-02-toggle-behavior.js

Confirm Page

- FrontSamples/front02/jquery-03-confirm-behavior.html
- FrontSamples/front02/css/jquery-03-confirm-behavior.css
- FrontSamples/front02/js/jquery-03-confirm-behavior.js

Popup

- FrontSamples/front02/jquery-04-popup-behavior.html
- FrontSamples/front02/js/jquery-04-popup-behavior.js

Ajax

- FrontSamples/front02/jquery-05-ajax-sample-behavior.html
- FrontSamples/front02/js/jquery-05-ajax-sample-behavior.js