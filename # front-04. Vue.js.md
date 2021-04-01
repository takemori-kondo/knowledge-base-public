# front-04. Vue.js
________________________________________
## 1. Prepare
________________________________________
Download by npm

```shell
cd front04
npm init -y
npm install vue --save
```

Download developer tool for chrome

```text
vue-devtools
```

________________________________________
## 2. Vue Tutorial
________________________________________
### 2.1. Vue Bind

front04/vue-01-bind.html

```html
<!DOCTYPE html>
<html lang="ja">

<head>
    <link rel="canonical" href="https://site.domain/">
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="keywords" content="keyword1,keyword2">
    <meta name="description" content="Search engine shows this text.">
    <meta name="author" content="Name; e-mail: name@mail.domain">
    <meta name="copyright" content="Copyright (c) Name All Rights Reserved.">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0 viewport-fit=cover">
    <script src="./node_modules/vue/dist/vue.js"></script>
    <title>vue-01-bind</title>
</head>

<body>
    <p>vue.jsで間違えやすい事１：bodyに直接バインドはできません。bodyの直下全体をdivで囲んでdivにバインドすればOKです。</p>
    <p>vue.jsで間違えやすい事２：computedやmethodsやwatchで、() => {...} による関数記述は許可されていません。</p>
    <p>vue.jsで間違えやすい事３：elプロパティに指定するのはセレクタです。idに対してであれば、#が必要です。</p>
    <div id="example">
        <div>example-01 本文は二重中括弧 : {{ message }}</div>
        <div>example-02 属性はv-bind（:のみによる略記表記が可能） : <span :title="message">Hover here.</span></div>
        <div>example-03 属性はv-bind（:のみによる略記表記が可能）classにバインドする場合はjson形式で記述できる : <span :class="classJson">Please show F12 >
                Elements.</span></div>
        <div>example-04 v-once : <span v-once>{{ message }}</span></div>
        <div>example-05 v-html（v-htmlはユーザーから提供されたコンテンツに対しては決して使用してはいけません。）: <span v-html="htmlMessage"></span></div>
        <div>example-06 v-if, v-else, key特殊属性 : <span v-if="seen" key="seenIsTrue">Now you see me</span><span v-else  key="seenIsFalse">Else!</span></div>
        <div>example-07 v-for : <ol>
                <li v-for="todo in todos">
                    {{ todo.text }}
                </li>
            </ol>
        </div>
        <div>example-08 v-on:clickとmethods（@のみによる略記表記が可能） : <button @click="reverseSeen">Reverse seen</button></div>
        <div>example-09 v-model（双方向バインディング） : <input v-model="message"></div>
        <div>example-10-1 elプロパティは、CSSセレクタを記載する</div>
        <div>example-10-2 dataプロパティは、バインドするjsonを設定する</div>
        <div>example-10-3 createdハンドラは、初期化時に呼ばれる（vue の createdを参照のこと）</div>
        <div>example-10-4 computedプロパティは、dataをoopフィールドと見立てた場合のoopプロパティである。キャッシュされる計算値 : {{ reversedMessage }}</div>
        <div>example-10-5 methodsプロパティは、dataをoopフィールドと見立てた場合のoopメソッドである。</div>
        <div>example-10-6 watchプロパティは、dataやcomputedに対する変更監視ができる。（vue の watchを参照のこと）</div>
        <div>example-11 templateタグ（html5にあるjsとの連携を前提とした初期では非表示な内容）とv-ifを組み合わせれば、簡単にdivで囲まずに複数の要素の表示切替ができる
            <template v-if="seen">
                <p>paragraph1</p>
                <p>paragraph2</p>
                <p>paragraph3</p>
            </template>
        </div>
    </div>
    <script>
        // ES2015
        // vue.js 2.x
        // インスタンスプロパティまたはコールバックでアロー関数 を使用しないでください。
        const example = new Vue({
            el: '#example',
            data: {
                message: 'Hello Vue!',
                htmlMessage: '<span style="color:blue">This text is blue!</span>',
                seen: true,
                todos: [{
                        text: 'Learn JavaScript'
                    },
                    {
                        text: 'Learn Vue'
                    },
                    {
                        text: 'Build something awesome'
                    }
                ],
                classJson: {
                    'text-left': true,
                    'text-danger': false
                }
            },
            created: function () {
                alert('created is called.');
            },
            computed: {
                reversedMessage: function () {
                    // `this` は この Vue インスタンスを指します
                    return this.message.split('').reverse().join('')
                }
            },
            methods: {
                reverseSeen: function () {
                    this.seen = (this.seen == false) ? true : false;
                }
            },
            watch: {
                seen: function (newValue, oldvalue) {
                    alert('seen : ' + oldvalue + '->' + newValue);
                }
            }
        });
    </script>
</body>

</html>
```

________________________________________
### 2.2. Vue Component (Customized Tag)

front04/vue-02-component.html

```html
<!DOCTYPE html>
<html lang="ja">

<head>
    <link rel="canonical" href="https://site.domain/">
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="keywords" content="keyword1,keyword2">
    <meta name="description" content="Search engine shows this text.">
    <meta name="author" content="Name; e-mail: name@mail.domain">
    <meta name="copyright" content="Copyright (c) Name All Rights Reserved.">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0 viewport-fit=cover">
    <script src="./node_modules/vue/dist/vue.js"></script>
    <title>vue-02-component</title>
</head>

<body>
    <ol id="sample-7">
        <component-1></component-1>
        <component-1></component-1>
        <component-1></component-1>
    </ol>
    <ol id="sample-8">
        <component-2 v-for="item in groceryList" v-bind:key="item.id" v-bind:todo="item"></component-2>
    </ol>
    <script>
        // ES2015
        // vue.js 2.x
        Vue.component('component-1', {
            template: '<li>This is a todo<\/li>'
        });
        const sample7 = new Vue({
            el: '#sample-7'
        });
        Vue.component('component-2', {
            props: ['todo'],
            template: '<li>{{ todo.id }} : {{ todo.text }}<\/li>'
        });
        // Let's type "sample8.groceryList.push({ text: 'New item' })" into "Chrome -> F12 -> Console"
        const sample8 = new Vue({
            el: '#sample-8',
            data: {
                groceryList: [{
                        id: 0,
                        text: 'Vegetables'
                    },
                    {
                        id: 1,
                        text: 'Cheese'
                    },
                    {
                        id: 2,
                        text: 'Whatever else humans are supposed to eat'
                    }
                ]
            }
        });
    </script>
</body>

</html>
```