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

### II. 对Nginx进行配置

nginx配置文件是`C:\WNMP\nginx\conf`文件夹里的nginx.conf

**修改大概第37行的监听端口**

```
listen       900;
```

**修改html文件和PHP文件路径**

```
# 45行左右
location / {
    # root   html;         --> 这里改成你自己的目录
    root   C:/wamp/www;
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
    # root   html;        --> 这里改成你自己的目录
    root           C:/wamp/www;
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

### III PHP安装及配置

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
php-cgi.exe -b 127.0.0.1:9000-c
```



