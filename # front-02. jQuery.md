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
### 2.1. Prop

front02/jquery-01-prop-sample.html

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
    <script src="./node_modules/jquery/dist/jquery.min.js"></script>
    <title>jquery-01-prop-sample</title>
</head>

<body>
    <button type="button" class="js-mode-changer" tabindex="100">ボタン</button>
    <form method="POST">
        <fieldset>
            <input type="text" name="input1" class="js-c1" value="js-c1 prop('disabled') to attr, attr to prop('disabled')" tabindex="1" size="100"><br>
            <input type="text" name="input2" class="js-c2" value="js-c2 prop('disabled') to prop, prop to prop('disabled')" tabindex="2" size="100"><br>
            <input type="text" name="input3" class="js-c3" value="js-c3 prop('disabled') to data, data to prop('disabled')" tabindex="3" size="100"><br>
            <input type="text" name="input4" class="js-c4" value="js-c4 prop('disabled') to attr, prop to prop('disabled')" tabindex="4" size="100"><br>
            <input type="text" name="input5" class="js-c5" value="js-c5 prop('disabled') to attr, data to prop('disabled')" tabindex="5" size="100"><br>
            <input type="text" name="input6" class="js-c6" value="js-c6 prop('disabled') to prop, data to prop('disabled')" tabindex="6" size="100"><br>
            <input type="text" name="input7" class="js-c7" value="js-c7 prop('disabled') to data, prop to prop('disabled')" tabindex="7" size="100"><br>
        </fieldset>
    </form>
    <script>
        // ES2015
        // jQuery 3.x
        $(() => $('.js-mode-changer').on('click', () => {

            // js-c1 prop('disabled') to attr, attr to prop('disabled')
            {
                const selector = '.js-c1';
                const disabled = 'disabled';
                const storeName = 'data-js-c1-store';
                const jq = $(selector);
                if (!jq.prop(disabled)) {
                    jq.attr(storeName, jq.prop(disabled));
                    jq.prop(disabled, true);
                }
                else {
                    jq.prop(disabled, jq.attr(storeName));
                }
            }

            // js-c2 prop('disabled') to prop, prop to prop('disabled')
            {
                const selector = '.js-c2';
                const disabled = 'disabled';
                const storeName = 'data-js-c2-store';
                const jq = $(selector);
                if (!jq.prop(disabled)) {
                    jq.prop(storeName, jq.prop(disabled));
                    jq.prop(disabled, true);
                }
                else {
                    jq.prop(disabled, jq.prop(storeName));
                }
            }

            // js-c3 prop('disabled') to data, data to prop('disabled')
            {
                const selector = '.js-c3';
                const disabled = 'disabled';
                const storeName = 'data-js-c3-store';
                const jq = $(selector);
                if (!jq.prop(disabled)) {
                    jq.data(storeName, jq.prop(disabled));
                    jq.prop(disabled, true);
                }
                else {
                    jq.prop(disabled, jq.data(storeName));
                }
            }

            // js-c4 prop('disabled') to attr, prop to prop('disabled')
            {
                const selector = '.js-c4';
                const disabled = 'disabled';
                const storeName = 'data-js-c4-store';
                const jq = $(selector);
                if (!jq.prop(disabled)) {
                    jq.attr(storeName, jq.prop(disabled));
                    jq.prop(disabled, true);
                }
                else {
                    jq.prop(disabled, jq.prop(storeName));
                }
            }

            // js-c5 prop('disabled') to attr, data to prop('disabled')
            {
                const selector = '.js-c5';
                const disabled = 'disabled';
                const storeName = 'data-js-c5-store';
                const jq = $(selector);
                if (!jq.prop(disabled)) {
                    jq.attr(storeName, jq.prop(disabled));
                    jq.prop(disabled, true);
                }
                else {
                    jq.prop(disabled, jq.data(storeName));
                }
            }

            // js-c6 prop('disabled') to prop, data to prop('disabled')
            {
                const selector = '.js-c6';
                const disabled = 'disabled';
                const storeName = 'data-js-c6-store';
                const jq = $(selector);
                if (!jq.prop(disabled)) {
                    jq.prop(storeName, jq.prop(disabled));
                    jq.prop(disabled, true);
                }
                else {
                    jq.prop(disabled, jq.data(storeName));
                }
            }

            // js-c7 prop('disabled') to data, prop to prop('disabled')
            {
                const selector = '.js-c7';
                const disabled = 'disabled';
                const storeName = 'data-js-c7-store';
                const jq = $(selector);
                if (!jq.prop(disabled)) {
                    jq.data(storeName, jq.prop(disabled));
                    jq.prop(disabled, true);
                }
                else {
                    jq.prop(disabled, jq.prop(storeName));
                }
            }
        }));
    </script>
</body>

</html>
```

________________________________________
### 2.2. Toggle

front02/jquery-02-toggle-behavior.html

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
    <script src="./node_modules/jquery/dist/jquery.min.js"></script>
    <script src="./js/jquery-02-toggle-behavior.js"></script>
    <title>jquery-02-toggle-behavior</title>
</head>

<body>
    <div>
        <button type="button" class="js-element-generator" tabindex="5">要素の動的生成</button>
        <button type="button" class="js-toggle-button" tabindex="10">Toggleボタン</button>
        <div class="js-toggle-target" style="display:none;">
            <p>ここは文章の段落です。ここは文章の段落です。ここは文章の段落です。ここは文章の段落です。</p>
            <p>ここは文章の段落です。ここは文章の段落です。ここは文章の段落です。ここは文章の段落です。</p>
            <p>ここは文章の段落です。ここは文章の段落です。ここは文章の段落です。ここは文章の段落です。</p>
        </div>
    </div>
    <script>
        // ES2015
        // jQuery 3.x
        $(() => {
            attachToggleBehavior('.js-toggle-button', '.js-toggle-target', 'slow');
            let num = 1;
            $('.js-element-generator').on('click', () => {
                const mod = num % 3;
                const toggleType =
                    mod == 1 ? "fade" :
                    mod == 2 ? "slide" :
                    "";
                const ti = (10 + num);
                const tb = 'js-toggle-button' + num;
                const tt = 'js-toggle-target' + num;
                const str = `
                <button class="${tb}" type="button" tabindex="${ti}">${toggleType}Toggleボタン<\/button>
                <div class="${tt}" style="display:none;">
                    <p>動的に追加された文章です。動的に追加された文章です。動的に追加された文章です。<\/p>
                    <p>動的に追加された文章です。動的に追加された文章です。動的に追加された文章です。<\/p>
                    <p>動的に追加された文章です。動的に追加された文章です。動的に追加された文章です。<\/p>
                <\/div>`;
                $("#root").append(str);                
                attachToggleBehavior("." + tb, "." + tt, 'slow', toggleType);
                num++;
            });
        });
    </script>
</body>

</html>
```

front02/jquery-02-toggle-behavior.js

```js
// ES2015
// jQuery 3.x

"use strict";

function attachToggleBehavior(buttonSelector, targetSelector, duration = 'normal', toggleType = 'toggle') {
    $(buttonSelector).on('click', () => {
        const jqButton = $(buttonSelector);
        const pre = buttonSelector.slice(0, 1);
        const buttonSelectorWithoutPre = (pre == '#' || pre == '.') ? buttonSelector.substr(1) : buttonSelector;
        const closed = buttonSelectorWithoutPre + '-closed';
        const opened = buttonSelectorWithoutPre + '-opened';
        const jqTarget = $(targetSelector);
        if (jqButton.hasClass(closed) || jqTarget.css('display') == 'none') {
            jqButton.removeClass(closed).addClass(opened);
        } else {
            jqButton.removeClass(opened).addClass(closed);
        }
        const lowerToggleType = toggleType.toLocaleLowerCase();
        if (0 <= lowerToggleType.indexOf('fade')) {
            jqTarget.fadeToggle(duration);
        } else if(0 <= lowerToggleType.indexOf('slide')) {
            jqTarget.slideToggle(duration);
        } else {
            jqTarget.toggle(duration);
        }
    });
}
```

________________________________________
### 2.3. Confirm Page

front02/jquery-03-confirm-behavior.html

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
    <link rel="stylesheet" type="text/css" href="./css/jquery-03-confirm-behavior.css">
    <script src="./node_modules/jquery/dist/jquery.min.js"></script>
    <script src="./js/jquery-03-confirm-behavior.js"></script>
    <title>jquery-03-confirm-behavior</title>
</head>

<body>
    <button type="button" class="js-mode-changer" tabindex="5">ボタン</button>
    <form method="POST">
        <fieldset>
            <legend>入力項目</legend>
            <input type="hidden" name="mode" class="js-mode" value="new">
            <div class="input required js-input">
                <label for="name" class="lbl">名前</label>
                <input type="text" name="name" id="name" class="txt" value="" tabindex="10" required="required" placeholder="名前を入れてください">
            </div>
            <div class="input email required js-input">
                <label for="email" class="lbl">メール</label>
                <input type="email" name="email" id="email" class="txt" value="" tabindex="20" required="required" placeholder="メールアドレスを入れてください">
            </div>
            <div class="input radio js-input">
                <label for="sex-1" class="lbl">性別</label>
                <input type="radio" name="sex" id="sex-1" value="男" tabindex="30" checked>
                <label for="sex-1">男</label>
                <input type="radio" name="sex" id="sex-2" value="女" tabindex="40">
                <label for="sex-2">女</label>
            </div>
            <div class="input select js-input">
                <label for="inquiry-type" class="lbl">お問い合わせ種類</label>
                <select name="inquiry-type" id="inquiry-type" class="slc" tabindex="50">
                    <option value="お問い合わせ種類1" selected="selected">お問い合わせ種類1</option>
                    <option value="お問い合わせ種類2">お問い合わせ種類2</option>
                    <option value="お問い合わせ種類3">お問い合わせ種類3</option>
                </select>
            </div>
            <div class="input textarea required js-input">
                <label for="inquiry-details" class="lbl">お問い合わせ内容</label>
                <textarea name="inquiry-details" id="inquiry-details" class="txt" cols="30" rows="10" tabindex="60" accesskey="t" required="required">例：〇〇サービスの××について</textarea>
            </div>
        </fieldset>
        <button type="submit" tabindex="70" accesskey="s">送信</button>
    </form>
    <script>
        // ES2015
        // jQuery 3.x
        $(() => $('.js-mode-changer').on('click', () => {
            const jqMode = $('.js-mode');
            const nextValue = jqMode.val() == 'confirm' ? 'new' : 'confirm';
            jqMode.val(nextValue);
            attachConfirmBehavior('input-confirm', '.js-mode', '.js-input');
        }));
    </script>
</body>

</html>
```

front02/jquery-03-confirm-behavior.css

```css
.lbl {
    display: block;
}

.lbl.input-confirm {
    border-color: transparent;
}

.input {
    margin-bottom: 1rem;
}

.txt {
    font: 1rem sans-serif;
    min-width: 300px;
    width:50%;
    box-sizing: border-box;
    border: 1px solid #999;
}

.txt.input-confirm {
    border-color: transparent;
    resize: none;
}

.slc {
    font: 1rem sans-serif;
    min-width: 300px;
    width:50%;
    box-sizing: border-box;
    border: 1px solid #999;
}

.slc.input-confirm {
    box-sizing: border-box;
    border: 1px solid transparent;
    padding:1px 4px;
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
}
```

front02/jquery-03-confirm-behavior.js

```js
// ES2015
// jQuery 3.x

"use strict";

function attachConfirmBehavior(confirmModeCssClass, modeSelector, inputContainerSelector, hidesEmpty = true) {
    const isConfirm = $(modeSelector).val() == 'confirm';
    if (hidesEmpty) {
        $(inputContainerSelector).each((i, element) => {
            toggleDisplayForEmptyElement(element, 'input', isConfirm);
            toggleDisplayForEmptyElement(element, 'select', isConfirm);
            toggleDisplayForEmptyElement(element, 'textarea', isConfirm);
        });
    }
    toggleReadOnlyAndDisabled(`${inputContainerSelector} input`, confirmModeCssClass, isConfirm);
    toggleReadOnlyAndDisabled(`${inputContainerSelector} select`, confirmModeCssClass, isConfirm);
    toggleReadOnlyAndDisabled(`${inputContainerSelector} textarea`, confirmModeCssClass, isConfirm);
}

function toggleDisplayForEmptyElement(containerElement, targetSelector, isConfirm) {
    const dcd = 'data-css-display';
    const jqContainer = $(containerElement);
    if (jqContainer.find(targetSelector).val() == '') {
        if (isConfirm) {
            jqContainer.data(dcd, jqContainer.css('display'));
            jqContainer.hide();
        } else {
            jqContainer.css('display', $(containerElement).data(dcd));
            jqContainer.data(dcd, '');
        }
    }
}

function toggleReadOnlyAndDisabled(targetSelector, confirmModeCssClass, isConfirm) {
    const dr = 'data-readonly';
    const dd = 'data-disabled';
    $(targetSelector).each((i, element) => {
        const jqElement = $(element);
        if (isConfirm) {
            jqElement.data(dr, jqElement.prop('readonly'));
            jqElement.prop('readonly', true);
            if (jqElement.prop('type') == 'radio' && !jqElement.prop('checked')) {
                jqElement.data(dd, jqElement.prop('disabled'));
                jqElement.prop('disabled', true);
            } else if (jqElement.prop('tagName') == 'SELECT') {
                const x = jqElement.find(':not(:selected)');
                x.data(dd, x.prop('disabled'));
                x.prop('disabled', true);
            }
            jqElement.addClass(confirmModeCssClass);
        } else {
            jqElement.prop('readonly', jqElement.data(dr));
            jqElement.data(dr, '');
            if (jqElement.prop('type') == 'radio' && !jqElement.prop('checked')) {
                jqElement.prop('disabled', jqElement.data(dd));
                jqElement.data(dd, '');
            } else if (jqElement.prop('tagName') == 'SELECT') {
                const x = jqElement.find(':not(:selected)');
                x.prop('disabled', x.data(dd));
                x.data(dd, '');
            }
            jqElement.removeClass(confirmModeCssClass);
        }
    });
}
```

________________________________________
### 2.4. Popup

front02/jquery-04-popup-behavior.html

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
    <script src="./node_modules/jquery/dist/jquery.min.js"></script>
    <script src="./node_modules/block-ui/jquery.blockUI.js"></script>
    <script src="./js/jquery-04-popup-behavior.js"></script>
    <title>jquery-04-popup-behavior</title>
</head>

<body>
    <button type="button" class="js-dialog-button" tabindex="10">Dialogボタン</button>
    <div class="js-dialog-contnt" style="display:none;">
        <p>ここは文章の段落です。ここは文章の段落です。ここは文章の段落です。ここは文章の段落です。</p>
        <p>ここは文章の段落です。ここは文章の段落です。ここは文章の段落です。ここは文章の段落です。</p>
        <p>ここは文章の段落です。ここは文章の段落です。ここは文章の段落です。ここは文章の段落です。</p>
        <form>
            <button type="button" class="js-ok" value="ok" tabindex="20">OK</button>
            <button type="button" class="js-cancel" value="cancel" tabindex="30">キャンセル</button>
        </form>
    </div>
    <button type="button" class="js-growl-button" tabindex="40">Growlボタン</button>
    <div class="js-growl-contnt" style="display:none;">
        グロールです。
    </div>
    <script>
        $(() => {
            const callback = (clickedSelector) => {
                if (clickedSelector == '.js-ok') {
                    alert("OK");
                }
            };
            attachDialogBehavior('.js-dialog-button', '.js-dialog-contnt', 5000, callback);
            attachGrowlBehavior('.js-growl-button', '.js-growl-contnt', 2000);
        });
    </script>
</body>

</html>
```

front02/jquery-04-popup-behavior.js

```js
// ES2015
// jQuery 3.x
// jquery.blockUI.js

"use strict";

function attachDialogBehavior(buttonSelector, contentSelector, timeout = null, callback = null, innerButtonSelectors = ['.js-ok', '.js-cancel']) {
    $(buttonSelector).on('click', () => {
        $.blockUI({
            message: $(contentSelector),
            timeout: timeout,
            css: {
                top: '20%'
            }
        });
    });
    for (const slctr of innerButtonSelectors) {
        $(slctr).on('click', () => {
            if (callback != null) {
                callback(slctr);
            }
            $.unblockUI();
        });
    }
}

function attachGrowlBehavior(buttonSelector, contentSelector, timeout = null) {
    $(buttonSelector).on('click', () => {
        $.blockUI({
            message: $(contentSelector),
            fadeIn: 700,
            fadeOut: 700,
            timeout: timeout,
            showOverlay: false,
            centerY: false,
            css: {
                width: '350px',
                top: '10px',
                left: '',
                right: '10px',
                border: 'none',
                padding: '5px',
                backgroundColor: '#000',
                '-webkit-border-radius': '10px',
                '-moz-border-radius': '10px',
                opacity: .6,
                color: '#fff'
            }
        });
    });
}
```

________________________________________
### 2.5. Ajax

front02/jquery-05-ajax-sample-behavior.html

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
    <script src="./node_modules/jquery/dist/jquery.min.js"></script>
    <script src="./node_modules/block-ui/jquery.blockUI.js"></script>
    <script src="./js/jquery-05-ajax-sample-behavior.js"></script>
    <title>jquery-05-ajax-sample-behavior</title>
</head>

<body>
    <button type="button" class="js-get-event-list-button" tabindex="10" accesskey="b">ボタン</button>
    <div class="js-event-list"></div>
    <script>
        // ES2015
        // jQuery 3.x
        $(() => attachConnpassEventGetterBehavior('.js-get-event-list-button', '.js-event-list'));
    </script>
</body>

</html>
```

front02/jquery-05-ajax-sample-behavior.js

```js
// ES2015
// jQuery 3.x
// jquery.blockUI.js

"use strict";

function attachConnpassEventGetterBehavior(buttonSelector, targetSelector) {
    // https://connpass.com/about/api/
    $(buttonSelector).on('click', () => {
        $.blockUI({
            message: null
        });
        const queryString = getConnpassRequestQueryString('東京', 3, 2, 100);
        $.ajax({
                url: 'http://connpass.com/api/v1/event/?' + queryString,
                type: 'GET',
                dataType: 'jsonp',
                jsonp: 'callback'
            })
            .done((data, textStatus, jqXHR) => {
                // success
                const formattedDataAndCaptions = formatConnpassEventData(data.events, true);
                const outputText = arrayToTableElement(formattedDataAndCaptions.formattedData, formattedDataAndCaptions.captionAndColumns);
                $(targetSelector).empty().append(outputText);
            })
            .fail((jqXHR, textStatus, errorThrown) => {
                // error
                alert('fail');
            })
            .always((arg1, textStatus, arg3) => {
                // complete
                $.unblockUI();
            });
    });
}

function getConnpassRequestQueryString(keyword, days, order, count) {
    const nows = new Array();
    nows[0] = new Date();
    for (let i = 1; i < days; i++) {
        nows[i] = new Date(nows[0].getTime());
        nows[i].setDate(nows[0].getDate() + i);
    }
    let ymdQuery = '';
    for (let i = 0; i < days; i++) {
        ymdQuery += '&ymd=' + nows[i].getFullYear() + ('0' + (nows[i].getMonth() + 1)).slice(-2) + nows[i].getDate();
    }
    return 'keyword=' + keyword + ymdQuery + '&order=' + order + '&count=' + count;
}

function formatConnpassEventData(events, isReverse = false) {
    const formattedData = new Array();
    for (const event of events) {
        const row = new Array();
        row['date'] = new Date(event.started_at).toLocaleString();
        row['title'] = '<a href="' + event.event_url + '">' + event.title + '</a>';
        row['limit'] = event.accepted + ' / ' + event.limit;
        formattedData.push(row);
    }
    if (isReverse) {
        formattedData.reverse();
    }
    return {
        'formattedData': formattedData,
        'captionAndColumns': {
            '日付': 'date',
            'タイトル': 'title',
            '規模': 'limit'
        }
    };
}

function arrayToTableElement(records, captionsAndColumns) {
    let outputText =`
<table>
    <tr>`;
    for (const key in captionsAndColumns) {
        outputText += `
        <th>${key}</th>`;
    }
    outputText += `
    </tr>`;
    for (const key1 in records) {
        outputText += `
    <tr>`;
        for (const key2 in captionsAndColumns) {
            outputText += `
        <td>${records[key1][captionsAndColumns[key2]]}</td>`;
        }
        outputText += `
    </tr>`;
    }
    outputText += `
</table>`;
    return outputText;
}
```
