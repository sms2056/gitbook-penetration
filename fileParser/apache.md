# Apache解析漏洞

---

## 一. Apache安装

* Linux + Apache + Mysql + PHP

* XAMPP\(WIndows\)

* LAMPP\(Linux\)

* WampServer\(Linux\)

## 二. Apache文件解析漏洞

| 应用 | 包含组件 |
| :--- | :--- |
| WampServer2.0a | Apache 2.2.6 |
|  | PHP      5.2.5 |
|  | MySQL 5.0.45 |
| Windows 2008 |  |

### 1. 多后缀名 {#1多后缀名}

Apache认为，一个文件可以有多个后缀，如：werner.txt.png.mp3。这一文件，放在Windows里，毫无疑问，就是个mp3文件，Windows只认最后一个“.”及其后面的字符“mp3”，觉得该文件后缀为“.mp3”，这也是大多数操作系统、应用软件的处理方式、是正常人习惯。

而在Apache中，则可能有所不同，如果有必要，Apache会从后（右）往前（左），一一辨别后缀。当Apache不认识某个后缀时，如某文件名为：werner.mp3.html.qwe.arex，Apache在处理时，先读取最后一个后缀，为“.arex”，一看，这啥玩意啊，不认识，继续读取下一个后缀“.qwe”，一看，呀，这又是啥，还是不认识，继续读下一个后缀“.html”，一看，哦，这是个超文本标记语言文件，俗称网页文件，这回认识了，也就不继续读下一个后缀了。

若是所有后缀都看完了没有一个认识怎么办？此时就会把该文件当做默认类型进行处理了，一般来说，默认类型是text/plain。

```py
# httpd.conf --> go line 355
#
# DefaultType: the default MIME type the server will use for a document
# if it cannot otherwise determine one, such as from filename extensions.
# If your server contains mostly text or HTML documents, "text/plain" is
# a good value.  If most of your content is binary, such as applications
# or images, you may want to use "application/octet-stream" instead to
# keep browsers from trying to display binary files as though they are
# text.
#
DefaultType text/plain
```

哪些后缀Apache认识，哪些不认识？在\`apache/conf\\`中存在一个名为mime.types的文件，其中记录着Apache能够识别的后缀。

在Ubuntu下，该文件位于/etc/mime.types.

在Windows下，该文件位于C:/apache/conf/mime.types（类似这样的，注意Apache的安装路径）。

该文件是一个一对多的映射表，定义了某一种文件类型，对应的几种后缀。

![](/fileParser/image/apache-mime.png)

除了该文件，在Apache的配置文件中，还可以用AddType语句添加映射，如：

![](/fileParser/image/apache-add-mime.png)

这一特性会带来什么问题呢？网站往往有上传文件的功能，但一定不想让用户上传程序，因为这很可能会危害网站安全，故而会检查上传文件的后缀名，若是.php，则拒绝上传（假设这是个php站）。此时用户只需上传文件evildoer.php.qwe，若是程序员不了解Apache的这一特性，编写的程序检查后缀时只看“.qwe”，而认为这不是程序文件，允许上传，则用户成功地绕过了上传时的安全检查，上传了php程序文件。该文件的最后一个后缀“.qwe”是Apache不认识的，故而Apache会以倒数第二个后缀“.php”为准，把该文件当做是php文件，解析执行。

这总是奏效的吗？按理来说，由于这是特性而不是漏洞，所以适用于所有版本的Apache。

我们来做个实验。准备一个文件，内容随意，命名为test.php.aaa，放置在Apache中，然后在浏览器中访问它，结果如下图所示：

```php
# test.php.aaa
<?php
    phpinfo();
?>
```

![](/fileParser/image/apache-testphp.png)我们来做个实验。准备一个图片，内容随意，命名为test.jpg.aaa，放置在Apache中，然后在浏览器中访问它，结果如下图所示：![](/fileParser/image/apache-testjpg.png)可见浏览器是将该文件作为图片处理的。浏览器为何认为test.jpg.aaa是图片呢？aaa可不是图片文件的后缀。这是因为服务器的响应HTTP头中的Content-Type字段值为image/jpeg，浏览器看到image/jpeg，便知这是图片文件。这说明服务器（此处即Apache）是把test.jpg.aaa当做图片的，也说明，前面分析的Apache的多后缀处理是没有错的。

### 2. 罕见后缀名 {#1多后缀名}

计算机世界自开天辟地以来，便自由多彩。还记得mime.types文件吗？在该文件中搜索“php”这三个字母，结果如下所示：

```asm
╭─sms2056@sms2056-ThinkPad-T460 /etc
╰─➤  cat /etc/mime.types | grep php
#application/x-httpd-php                        phtml pht php
#application/x-httpd-php-source                 phps
#application/x-httpd-php3                       php3
#application/x-httpd-php3-preprocessed          php3p
#application/x-httpd-php4                       php4
#application/x-httpd-php5                       php5
```

好吧，原来不仅php，就连phtml、pht、php3、php4和php5都是Apache和php认可的php程序的文件后缀。利用这些“罕见”的后缀名，也可能绕过安全检查，干些“坏事”。

还是使用我们的test.php.aaa重命名为test.phtml,我们来看看是否能够错误执行![](/fileParser/image/apache-php3.png)![](/fileParser/image/apache-php.png)错误执行成功.

三. Apache的安全配置

