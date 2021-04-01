# front-03. Bootstrap4
________________________________________
## 1. Prepare
________________________________________
Download by npm

```shell
cd front03
npm init -y
npm install jquery --save
npm install popper.js --save
npm install bootstrap --save
```

Summery

```text
1. bootstrapを適用すると、デフォルトのbox-sizingがborder-boxになる
2. カルーセルやナビゲーションなど、一通りそろっている
3. 公式リファレンスが充実しているので、そちらを見ること
```

________________________________________
## 2. Samples
________________________________________
### 2.1. Layout

front03/bootstrap-layout.html

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
    <link rel="stylesheet" type="text/css" href="./node_modules/bootstrap/dist/css/bootstrap.min.css">
    <script src="./node_modules/jquery/dist/jquery.min.js"></script>
    <script src="./node_modules/popper.js/dist/popper.min.js"></script>
    <script src="./node_modules/bootstrap/dist/js/bootstrap.min.js"></script>
    <title>Bootstrap layout</title>
    <style>
        .selector-for-some-widget {
            box-sizing: content-box;
        }
    </style>
</head>

<body>
    <h1>Container</h1>
    <h2>container 1</h2>
    <div class="container">
        <div class="row">
            <div class="col" style="background-color:aqua">
                2つの列の1列目
            </div>
            <div class="col" style="background-color:magenta">
                2つの列の2列目
            </div>
        </div>
        <div class="row">
            <div class="col" style="background-color:yellow">
                3つの列の1列目
            </div>
            <div class="col" style="background-color:lime">
                3つの列の2列目
            </div>
            <div class="col" style="background-color:orange">
                3つの列の3列目
            </div>
        </div>
    </div>
    <h2>container-fluid 1</h2>
    <div class="container-fluid">
        <div class="row">
            <div class="col" style="background-color:aqua">
                A
            </div>
            <div class="col-8" style="background-color:magenta">
                B:col-8(8/12)
            </div>
            <div class="col" style="background-color:yellow">
                C
            </div>
        </div>
        <div class="row">
            <div class="col" style="background-color:aqua">
                A
            </div>
            <div class="col-7" style="background-color:magenta">
                B:col-7
            </div>
            <div class="col" style="background-color:yellow">
                C
            </div>
        </div>
        <div class="row">
            <div class="col" style="background-color:aqua">
                A
            </div>
            <div class="col-6" style="background-color:magenta">
                B:col-6(6/12)
            </div>
            <div class="col" style="background-color:yellow">
                C
            </div>
        </div>
        <div class="row">
            <div class="col" style="background-color:aqua">
                A
            </div>
            <div class="col-5" style="background-color:magenta">
                B:col-5
            </div>
            <div class="col" style="background-color:yellow">
                C
            </div>
        </div>
        <div class="row">
            <div class="col" style="background-color:aqua">
                A
            </div>
            <div class="col-4" style="background-color:magenta">
                B:col-4(4/12)
            </div>
            <div class="col" style="background-color:yellow">
                C
            </div>
        </div>
        <div class="row">
            <div class="col" style="background-color:aqua">
                A
            </div>
            <div class="col-3" style="background-color:magenta">
                B:col-3
            </div>
            <div class="col" style="background-color:yellow">
                C
            </div>
        </div>
        <div class="row">
            <div class="col" style="background-color:aqua">
                A
            </div>
            <div class="col-2" style="background-color:magenta">
                B:col-2(2/12)
            </div>
            <div class="col" style="background-color:yellow">
                C
            </div>
        </div>
        <div class="row">
            <div class="col" style="background-color:aqua">
                A
            </div>
            <div class="col-1" style="background-color:magenta">
                B:col-1
            </div>
            <div class="col" style="background-color:yellow">
                C
            </div>
        </div>
    </div>
    <h2>container 2</h2>
    <div class="container" style="background-color:gray">
        <div class="row justify-content-start">
            <div class="col-4" style="background-color:aqua">
                A:col-4
            </div>
            <div class="col-4" style="background-color:magenta">
                B:col-4
            </div>
        </div>
        <div class="row justify-content-center">
            <div class="col-4" style="background-color:aqua">
                A:col-4
            </div>
            <div class="col-4" style="background-color:magenta">
                B:col-4
            </div>
        </div>
        <div class="row justify-content-end">
            <div class="col-4" style="background-color:aqua">
                A:col-4
            </div>
            <div class="col-4" style="background-color:magenta">
                B:col-4
            </div>
        </div>
        <div class="row justify-content-around">
            <div class="col-4" style="background-color:aqua">
                A:col-4
            </div>
            <div class="col-4" style="background-color:magenta">
                B:col-4
            </div>
        </div>
        <div class="row justify-content-between">
            <div class="col-4" style="background-color:aqua">
                A:col-4
            </div>
            <div class="col-4" style="background-color:magenta">
                B:col-4
            </div>
        </div>
    </div>
    <h2>container-fluid 2</h2>
    <div class="container-fluid" style="background-color:gray">
        <div class="row">
            <div class="col-4" style="background-color:aqua">
                A:col-4
            </div>
            <div class="col-4" style="background-color:magenta">
                B:col-4
            </div>
        </div>
        <div class="row">
            <div class="col-4" style="background-color:aqua">
                A:col-4
            </div>
            <div class="col-4 offset-1" style="background-color:magenta">
                B:col-4, offset-1
            </div>
        </div>
        <div class="row">
            <div class="col-4" style="background-color:aqua">
                A:col-4
            </div>
            <div class="col-4 offset-2" style="background-color:magenta">
                B:col-4, offset-2
            </div>
        </div>
        <div class="row">
            <div class="col-4" style="background-color:aqua">
                A:col-4
            </div>
            <div class="col-4 offset-3" style="background-color:magenta">
                B:col-4, offset-3
            </div>
        </div>
        <div class="row">
            <div class="col-4" style="background-color:aqua">
                A:col-4
            </div>
            <div class="col-4 offset-4" style="background-color:magenta">
                B:col-4, offset-4
            </div>
        </div>
    </div>
</body>

</html>
```
