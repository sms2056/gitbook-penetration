# WAMP和WNMP安装部署

---

## 一. WAMP\(windws + Apache + MySql + PHP\)

### ![](/fileParser/image/WAMP.png)

| 软件名称 | 优点 | 下载地址 |
| :---: | :--- | :---: |
| xampp | windows + linux | [下载](https://www.apachefriends.org/zh_cn/index.html) |
| wampserver | PHP版本可进行5/7更换 | [下载](http://www.wampserver.com/) |

## 二. WNMP\(windws + Nginx + MySql + PHP\)

![](/fileParser/image/WNMP.png)

| 软件名称 | 下载地址 |
| :--- | :---: |
| Nginx | [下载](http://nginx.org/en/download.html) |
| PHP | [下载](https://windows.php.net/download/) |
| MySQL | 这里使用WAMPserver自带的MySQL |
| phpMyAdmin | [下载](https://www.phpmyadmin.net/) |

东西准备完了，那么开始安装了。

### I. Nginx 安装

1. 准备一个目录， 我这里是 `c:\wnmp\nginx`（这里nginx目录下主要是为了以后方便拓展多版本的Nginx服务）

2. 解压下载的Nginx文件到上面所说目录

3. 运行该文件夹下的nginx.exe

4. 测试是否启动nginx。打开浏览器访问[http://localhost](http://localhost) 或[http://127.0.0.1](http://127.0.0.1，看看是否出现“Welcome)![](/fileParser/image/niginx-1.png)出现“Welcome to nginx!”，出现的证明已经启动成功了,没有启动的话，查看80端口是否被占用。   注意：该网站的默认目录在 E:\development\nginx\nginx-1.10.3\html

### II. 对Nginx进行支持CGI的配置

nginx配置文件是`C:\WNMP\nginx\conf`文件夹里的nginx.conf

**修改大概第37行的监听端口**

```
listen       900;
```

**修改html文件和PHP文件路径**

```
# 45行左右
location / {
    # root   html;         --> 这里改成你自己的html文件目录
    root   C:/wnmp/www;
    # index  index.html index.htm;
    index  index.html index.htm index.php;
}
```

```
# 67行左右
# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
#
#location ~ \.php$ {
#    root           html;
#    fastcgi_pass   127.0.0.1:9000;
#    fastcgi_index  index.php;
#    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
#    include        fastcgi_params;
#}
# 去除#并修改root和fastcgi_param
# location 正则匹配到以php结尾的到这里解析
location ~ \.php$ {
    # root   html;        --> 这里改成你自己的html文件目录
    root           C:/wnmp/www;
    # 指明了用哪里的php-fpm来解析
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;

    # /scripts修改为$document_root$,指向web文件目录
    # fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    # 指明的是php动态程序的主目录
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include        fastcgi_params;
}
```

启动Nginx或重启Nginx

### III. PHP安装及配置

**A. **创建一个PHP目录 E:\wnmp\php\(将下好的php解压到这里\)

**B. **将php.ini-development或php.ini-production重命名为php.ini

**C. **修改php.ini

搜索“extension\_dir”找到: `;xtension_dir = "ext"`

> 先去前面的分号再改为extension\_dir = "C:\wnmp\php\ext"

搜索“php\_mysql”找到:`”extension=php_pdo_mysql.dll`和`extension=php_mysqli.dll`

> 去掉前面的“;”extension=php\_mysql.dll和extension=php\_mysqli.dll

查找定位至`;extension=php_pdo_mysql.dll`，

> 将前面的分号去掉为`extension=php_pdo_mysql.dll`

查找定位至`;extension=php_mbstring.dll`

> 将前面的分号去掉为`extension=php_mbstring.dll`

查找定位至`;cgi.fix_pathinfo=1`，

> 将前面的分号去掉为`cgi.fix_pathinfo=1`

---

搜索“date.timezone”找到:`;date.timezone =`

> 先去前面的分号再改为 date.timezone = Asia/Shanghai

搜索“enable\_dl”，找到:`enable_dl = Off`

> 改为 enable\_dl = On

搜索“cgi.force\_redirect”找到:`;cgi.force_redirect = 1`

> 先去前面的分号再改为 cgi.force\_redirect = 0

搜索“fastcgi.impersonate”找到:`;fastcgi.impersonate = 1`

> 去掉前面的分号

搜索“cgi.rfc2616\_headers”找到:`;cgi.rfc2616_headers = 0`

> 先去前面的分号再改为 cgi.rfc2616\_headers = 1

查找定位至`;extension=php_gd2.dll`

> 将前面的分号去掉为`extension=php_gd2.dll`

**D. **启动php-cgi

```
C:/WNMP/php/php-cgi.exe -b 127.0.0.1:9000 -c c:\WNMP\php\php.ini
```

**测试Nginx+php是否成功**

在C:/wnmp/www目录创建phpinfo.php文件,文件内容为

```php
<?php
    phpinfo();
?>
```

![](/fileParser/image/nginxANDphp.png)

### VI. MySQL的使用

由于使用了WAMPserver自带的MySQL,直接启动WAMPserver即可

**测试Nginx+php+mysql是否成功**

在C:/wnmp/www目录创建mysql.php文件,文件内容为

```php
<?php
    $link = mysql_connect("localhost","root","");
    if($link)
    {
        echo "connect ok";
    }else
    {
        echo "connect error";
    }
?>
```

![](/fileParser/image/nginxANDphpANDmysql.png)

### V 联合启动

1. 下载[RunHiddenConsole](http://www.inbeijing.org/wp-content/uploads/2015/06/RunHiddenConsole.zip)

2. 将RunHiddenConsole解压到`c:\wnmp`目录

3. 创建start.bat文件

    @echo off
    REM Windows 下无效
    REM set PHP_FCGI_CHILDREN=5
    REM 每个进程处理的最大请求数，或设置为 Windows 环境变量
    set PHP_FCGI_MAX_REQUESTS=1000

    echo Starting PHP FastCGI...
    RunHiddenConsole C:/WNMP/php/php-cgi.exe -b 127.0.0.1:9000 -c c:\WNMP\php\php.ini

    echo Starting nginx...
    # -p 指向nginx目录,不要添加`/`
    # -c 为 nginx 的配置文件
    RunHiddenConsole C:/WNMP/nginx/nginx.exe -p C:/WNMP/nginx

1. 创建stop.bat文件

```
@echo off
echo Stopping nginx...  
taskkill /F /IM nginx.exe > nul
echo Stopping PHP FastCGI...
taskkill /F /IM php-cgi.exe > nul
exit
```

1. 双击start.bat启动服务

![](/fileParser/image/server-pro.png)

1. 双击stop.bat结束进程

### IV. phpMyAdmin安装与配置

a. 将下载好的phpMyAdmin解压到`C:/wnmp/www/phpmyadmin`

b. 将`C:/wnmp/www/phpmyadmin`中的`config.simple.inc.php`重命名为`config.inc.php`

c. 修改`config.inc.php文件`

```
$cfg['Servers'][$i]['AllowNoPassword'] = false;
修改为
$cfg['Servers'][$i]['AllowNoPassword'] = true;
```

d. 修改`C:\wnmp\www\phpMyAdmin\libraries`文件夹中的`config.default.php`

```
# 默认不动,或填写'localhost'
$cfg['Servers'][$i]['host'] = 'localhost';

# 填写Mysql的用户名和密码
$cfg['Servers'][$i]['user'] = 'root';
$cfg['Servers'][$i]['password'] = '';

# 设置允许空密码登录
$cfg['Servers'][$i]['nopassword'] = false;
```

e. 查看配置是否成功,浏览[http://127.0.0.1:900](http://127.0.0.1:900)

![](/fileParser/image/phpmyadmin_1.png)![](/fileParser/image/phpmyadmin_2.png)

---

## Nginx + FCGI运行原理



