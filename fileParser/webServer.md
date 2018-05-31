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

1. 准备一个目录， 我这里是 `c:\wnmp\nginx`（这里nginx目录下主要是为了以后方便拓展多版本的Nginx服务）

2. 运行该文件夹（nginx-1.10.3）下的nginx.exe

3.测试是否启动nginx。打开浏览器访问http://localhost 或http://127.0.0.1，看看是否出现“Welcome to nginx!”，出现的证明已经启动成功了。没有启动的话，看看80端口有占用没。

注意：该网站的默认目录在 E:\development\nginx\nginx-1.10.3\html 

