# Nginx文件解析漏洞

---

## ![](/fileParser/image/niginx-logo.jpg)一. 实验背景

如果PHP的配置文件里 `cgi.fix_pathinfo=1` 那么就会产生文件解析漏洞。这个配置默认是1的，若设为0会导致很多MVC框架（如Thinkphp）都无法运行。

这个漏洞就是比如`localhost/img/1.jpg`是正常地访问一张图片，而`localhost/img/1.jpg/1.php`却会把这张图片作为PHP文件来执行！

如下图，由于1.php没有找到,所以服务器本应该是返回404 NotFound 才对的，它却显示说是有语法错误。

![](/fileParser/image/nginx-FP_1.png)

## 二. 实验环境

| 环境 | 版本 |
| :--- | :--- |
| windows | server 2008 |
| nginx | 1.14.0 |
| php | 5.6.36 |

## 三. 实验步骤

### 实验一:

**利用方式:**

> http://127.0.0.1/nginx.jpg/nginx.php

a. 准备文件nginx.jpg，内容为：

```
<?php phpinfo() ?>
```

b. 在浏览器中访问`http://127.0.0.1/nginx.jpg`,显示图片解析错误。

![](/fileParser/image/nginx-FP_3.png)

c. 在浏览器中访问`http://127.0.0.1/nginx.jpg/nginx.php`居然执行了PHP代码

![](/fileParser/image/nginx-FP_4.png)

这就有意思了，nginx.jpg是文件不是目录，nginx.php更是根本就不存在的文件，访问`http://127.0.0.1/nginx.jpg/nginx.php`没有报404，而是正常显示代码.

### 实验二:

a. 准备一份jpg文件

![](/fileParser/image/test.jpg)

b. 使用010editor,对jpg文件进行二进制修改

  原图:

![](/fileParser/image/nginx-FP_5.png)

  修改后:

![](/fileParser/image/nginx-FP_6.png)

c. 在浏览器中访问`http://127.0.0.1/test.jpg`,显示图片解析错误。

d. 在浏览器中访问`http://127.0.0.1/test.jpg/test.php`PHP代码正常执行

