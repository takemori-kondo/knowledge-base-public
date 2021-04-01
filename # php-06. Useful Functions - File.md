# php-06. Useful Functions - File
________________________________________
## 1. File Permissions
________________________________________
Settings

- Check apache's umask. (e.g. if umask=022, 777 to 744)

File permissions

```php
// Check exists.
$isFoo = file_exists($path);

// Run mkdir.
mkdir($path, 774, true);

// Run chmod.
chmod($path, 774);
```

________________________________________
## 2. File Upload
________________________________________
File Uplaod

```php
// <input type="file" name="foo" />
if (isset($postData['foo']['name']) && !empty($postData['foo']['name'])) {
    if (!@move_uploaded_file($postData['foo']['tmp_name'], $dir . '/' . $fixedFileName)) {
        // Throw framework's exception.
    }
}
```
