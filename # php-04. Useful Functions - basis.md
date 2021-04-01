# php-04. Useful Functions - basis
________________________________________
## 1. Variable Control
________________________________________
Variable Declaration or status.

```php
// Declaration.
$a;

// Un-declaration.
unset($a);

// Declare constant.
define('FOO', 'This is foo.');

// Get all constant values as array.
$constArray = get_defined_constants(true)['user'];

// Check empty.
$isFoo = empty($variable);
```

Array

```php
// Initialize way 1.
$arr = array('key1' => 'value1', 'key2' => 'value2');

 // Initialize. way 2.
$arr = ['key1' => 'value1', 'key2' => 'value2'];

// Add.
array_push($collection, $element);

// Search key.
$isFoo = array_key_exists('key', $collection);

// Search value.
$isFoo = in_array('value', $collection);

// Concat.
$str = implode(',', $stringCollection);

// Create array by variable names.
$arr = compact('variable-name1', 'variable-name2', ...);

// Create vars by array.
extract($collection);
```

________________________________________
## 2. String
________________________________________
String

```php
// Replace, utf8 ok.
$str = str_replace(
    ['-', 'ｰ', '‐', '－', 'ー', '―'],
    ['', '', '', '', '', ''],
    $sourceString);

// Trim, utf8 ok.
$str = trim('文字列');

// Regex, utf8 ok.
$isFoo = preg_match($pattern, '文字列', $returnMatchedValues);

// Pad
echo str_pad("123", 5, "0", STR_PAD_LEFT); // 00123
```

Mb_string

```php
// Get length.
$int = mb_strlen('文字列');

// Get position index.
$int = mb_strpos('文字列', $keyword);

// Get sub-string.
$str = mb_substr('文字列', $index, $length);

// Get matched value for regex.
$str = mb_ereg($pattern, '文字列');

// Get replaced value for regex.
$str = mb_ereg_replace($pattern, $replace, '文字列');

// Get splitted array for regex.
$arr = mb_split($pattern, '文字列');

// To lower.
$str = mb_strtolower('Abc');

// To upper.
$str = mb_strtoupper('Abc');

// To han-kaku.
$str = mb_convert_kana('文字列', 'rnk', 'utf-8');

// To zen-kaku.
$str = mb_convert_kana('文字列', 'RNKV', 'utf-8');
```

________________________________________
## 3. Json
________________________________________
Json

```php
// Encode.
$str = json_encode($object);

// Decode.
$assoc = json_decode($string, true);
```
