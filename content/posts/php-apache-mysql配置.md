---
date: 2014-04-05T00:00:00
title: php apache mysql配置
categories:
  - note
tags:
  - apache
  - php
  - mysql
---

php apache mysql配置
<!--more-->

httpd.conf:

```
LoadModule php5_module "F:/software/apm/php54/php5apache2_2.dll"
AddType application/x-httpd-php .php
PHPIniDir "F:/software/apm/php54"
# Virtual hosts
Include conf/extra/httpd-vhosts.conf
DocumentRoot "F:/PhpProject/cars"
<Directory "F:/PhpProject/cars">
```

httpd-vhosts.conf:

``` xml
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host.macd.com
    DocumentRoot "E:/PhpProject/cars"
    ServerName 127.0.0.1
    #ServerAlias www.dummy-host.macd.com
    ErrorLog "logs/cars-error.log"
    CustomLog "logs/cars-access.log" common
</VirtualHost>
```

php.ini:

``` ini
extension=php_mysql.dll
extension=php_mysqli.dll
extension_dir = "F:/software/apm/php54/ext"
```
