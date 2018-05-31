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

> [http://127.0.0.1/nginx.jpg/nginx.php](http://127.0.0.1/nginx.jpg/nginx.php)

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

![](/fileParser/image/nginx-FP_7.png)

d. 在浏览器中访问`http://127.0.0.1/test.jpg/test.php`PHP代码正常执行

![](/fileParser/image/nginx-FP_8.png)

## 四. 原理解释

Nginx拿到文件路径（更专业的说法是URI）`/test.jpg/test.php`后，一看后缀是.php，便认为该文件是php文件，转交给php去处理。php一看`/test.jpg/test.php`不存在，便删去最后的`/test.php`，又看`/test.jpg`存在，便把`/test.jpg`当成要执行的文件了.

这其中涉及到php的一个选项：`cgi.fix_pathinfo`，该值默认为1，表示开启。开启这一选项有什么用呢？看名字就知道是对文件路径进行“修理”。何谓“修理”？举个例子，当php遇到文件路径“`/aaa.xxx/bbb.yyy/ccc.zzz`”时，若“`/aaa.xxx/bbb.yyy/ccc.zzz`”不存在，则会去掉最后的“`/ccc.zzz`”，然后判断“`/aaa.xxx/bbb.yyy`”是否存在，若存在，则把“`/aaa.xxx/bbb.yyy`”当做文件“`/aaa.xxx/bbb.yyy/ccc.zzz`”，若“`/aaa.xxx/bbb.yyy`”仍不存在，则继续去掉“`/bbb.yyy`”，以此类推。

该选项在配置文件php.ini中。若是关闭该选项，访问`http://127.0.0.1/test.jpg/test.php`只会返回找不到文件。但关闭该选项很可能会导致一些其他错误，所以一般是开启的。

这一漏洞是由于Nginx中php配置不当而造成的，与Nginx版本无关，但在高版本的php中，由于“security.limit\_extensions”的引入，使得该漏洞难以被成功利用。

为何是Nginx中的php才会有这一问题呢？因为Nginx只要一看URL中路径名以.php结尾，便不管该文件是否存在，直接交给php处理。而如Apache等，会先看该文件是否存在，若存在则再决定该如何处理。cgi.fix\_pathinfo是php具有的，若在php前便已正确判断了文件是否存在，cgi.fix\_pathinfo便派不上用场了，这一问题自然也就不存在了。

## 五. 意外\(Linux\)

因为新版本的php引入了“security.limit\_extensions”，限制了可执行文件的后缀，默认只允许执行.php文件。来做进一步测试。找到php5-fpm配置文件php-fpm.conf，若不知道在哪，可用如下命令搜索：

> sudo find / -name php-fpm.conf

该文件位于/etc/php5/fpm/php-fpm.conf。修改该文件中的“security.limit\_extensions”，添加上.jpg，添加后如下所示：

> security.limit\_extensions = .php .jpg

这样，php便认为.jpg也是合法的php文件后缀了，再在浏览器中访问`http://127.0.0.1/test.jpg/test.php`，看到php被成功执行，如下图所示：

![](/fileParser/image/nginx-FP_9.png)

## 六. %00截断

漏洞版本:

> 0.5.\*, 0.6.\*, 0.7 &lt;= 0.7.65, 0.8 &lt;= 0.8.37

利用方式：

> /test.jpg%00.php

服务器为Nginx0.7.69，浏览器中访问`http://127.0.0.1/test.jpg%00.php`，返回“400 Bad Request”，代码未执行，测试失败。

![](/fileParser/image/nginx-FP_10.png)

## 七. CVE-2013-4547

漏洞版本

> 0.8.41～1.4.3， 1.5 &lt;= 1.5.7

这一漏洞的原理是非法字符空格和截止符（\0）会导致Nginx解析URI时的有限状态机混乱，危害是允许攻击者通过一个非编码空格绕过后缀名限制。是什么意思呢？举个例子，假设服务器上存在文件：“file.aaa ”，注意文件名的最后一个字符是空格。则可以通过访问：

> [http://127.0.0.1/file.aaa](http://127.0.0.1/file.aaa) \0.bbb

让Nginx认为文件“file.aaa ”的后缀为“.bbb”。

来测试下，这次测试在Nginx/1.0.15中进行。首先准备一张图片，命名为“test.html ”，注意，文件名含有空格。然后在浏览器中访问该文件，会得到一个404，因为浏览器自动将空格编码为%20，服务器中不存在文件“test.html%20”。

测试目标是要让Nginx认为该文件是图片文件并正确地在浏览器中显示出来。我们想要的是未经编码的空格和截止符（\0），怎么办呢？使用Burp Suite抓取浏览器发出的请求包，修改为我们想要的样子，原本的URL是：`http://192.168.56.101/test.htmlAAAphp`,将第一个“A”改成“20”（空格符号的ASCII码），将第二个“A”改成“00”（截止符），将第三个“A”改成“2e”（“.”的ASCII码），如下图所示：

![](/fileParser/image/nginx-FP_11.png)

修改完毕后Forward该请求，在浏览器中看到：

![](/fileParser/image/nginx-FP_12.png)

我们已经成功地利用了漏洞！但这有什么用呢？我们想要的是代码被执行。

继续测试，准备文件“test.jpg ”，注意文件名的最后一个字符是空格，文件内容为：

```
<?php phpinfo() ?>
```

用Burp Suite抓包并修改，原本的URL是：`http://192.168.56.101/test.jpg…php`,将jpg后的第一个“.”改为20，第二个“.”改为00，如下图所示：![](/fileParser/image/nginx-FP_13.png)修改完毕后Forword该请求，在浏览器中看到：

> Access denied.

好吧，又是这个。打开Nginx的错误日志，在其中也可以看到：

> FastCGI sent in stderr: "Access to the script '/usr/local/nginx/html/test.jpg '

这说明Nginx在接收到这一请求后，确实把文件“test.jpg ”当做php文件交给php去执行了，只是php看到该文件后缀为“.jpg ”而拒绝执行。这样，便验证了Nginx确实存在该漏洞。

**CVE-2013-4547还可以用于绕过访问限制**

首先在网站根目录下新建一个目录，命名为protected，在目录protected中新建文件s.html，内容随意。然后在Nginx的配置文件中写上：

```
location /protected/ {
    deny all;
  }
```

以禁止该目录的访问。接着在网站根目录下新建一个目录，名为“test ”，目录名的最后一个字符是空格，该目录用于触发漏洞。最后来进行验证，直接访问：

> http://127.0.0.1/protected/s.html

返回“403 Forbidden”。利用漏洞访问：

> [http://127.0.0.1/test](http://127.0.0.1/test) /../protected/s.html

成功访问到文件s.html。注意上示URL中的空格，不要将空格编码。

