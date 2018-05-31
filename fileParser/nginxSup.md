# Nginx解析漏洞-补充
## 一. %00截断

漏洞版本:

> 0.5.\*, 0.6.\*, 0.7 &lt;= 0.7.65, 0.8 &lt;= 0.8.37

利用方式：

> /test.jpg%00.php

服务器为Nginx0.7.69，浏览器中访问`http://127.0.0.1/test.jpg%00.php`，返回“400 Bad Request”，代码未执行，测试失败。

![](/fileParser/image/nginx-FP_10.png)

## 二. CVE-2013-4547

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

> http://127.0.0.1/test /../protected/s.html

成功访问到文件s.html。注意上示URL中的空格，不要将空格编码。


