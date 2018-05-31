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

1. 修改大概第37行的监听端口

2. ```
   listen       900;
   ```

   2 . 修改html文件和PHP文件路径

```
location / {
    # root   html;
    root   C:/wamp/www;
    # index  index.html index.htm;
    index  index.html index.htm index.php;
}
```

```
# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
#
#location ~ \.php$ {
#    root           html;
#    fastcgi_pass   127.0.0.1:9000;
#    fastcgi_index  index.php;
#    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
#    include        fastcgi_params;
#}

location ~ \.php$ {
    root           C:/wamp/www;
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;
    # fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include        fastcgi_params;
}
```



