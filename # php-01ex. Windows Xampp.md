# php-01ex. Windows Xampp
________________________________________
## Xampp

1. Disable IIS
    1. Control Panel
    2. Programs and Features
    3. Turn Windows features on or off
    4. Internet Information Service
2. Download & unzip Xampp
    1. e.g. https://sourceforge.net/projects/xampp/files/XAMPP%20Windows/7.2.3/
3. Put it on C:\
4. Download and setup php X Debug TS 32bit
    1. e.g. https://xdebug.org/files/php_xdebug-2.6.0-7.2-vc15.dll
    2. Put it on <xampproot>\php\ext
    3. Add settings to <xampproot>\php\php.ini
5. Run xampp-control.exe, and start Apache & MySQL

```ini
[XDebug]
zend_extension = "C:\xampp\php\ext\php_xdebug-2.6.0-7.2-vc15.dll"
xdebug.profiler_append=0
xdebug.profiler_enable=0
xdebug.profiler_enable_trigger = 0
xdebug.profiler_output_dir="c:\xampp\tmp"
xdebug.remote_autostart = 1
xdebug.remote_cookie_expire_time = 36000
xdebug.remote_enable=1
xdebug.remote_handler="dbgp"
xdebug.remote_host="localhost"
xdebug.remote_port=9000
```

Confirm versions by shell of xampp

```shell
httpd -v
mysql -v
php -v
```

Supplement

1. Web server's document-root is htdocs.
2. Web site url is localhost.
