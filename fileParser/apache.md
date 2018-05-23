# Apache文件解析漏洞

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

```
     Apache认为，一个文件可以有多个后缀，如：werner.txt.png.mp3。这一文件，放在Windows里，毫无疑问，就是个mp3文件，Windows只认最后一个“.”及其后面的字符“mp3”，觉得该文件后缀为“.mp3”，这也是大多数操作系统、应用软件的处理方式、是正常人习惯。

    而在Apache中，则可能有所不同，如果有必要，Apache会从后（右）往前（左），一一辨别后缀。当Apache不认识某个后缀时，如某文件名为：werner.mp3.html.qwe.arex，Apache在处理时，先读取最后一个后缀，为“.arex”，一看，这啥玩意啊，不认识，继续读取下一个后缀“.qwe”，一看，呀，这又是啥，还是不认识，继续读下一个后缀“.html”，一看，哦，这是个超文本标记语言文件，俗称网页文件，这回认识了，也就不继续读下一个后缀了。

    若是所有后缀都看完了没有一个认识怎么办？此时就会把该文件当做默认类型进行处理了，一般来说，默认类型是text/plain。
```

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

哪些后缀Apache认识，哪些不认识？在\\`apache/conf\\`中存在一个名为mime.types的文件，其中记录着Apache能够识别的后缀。

在Ubuntu下，该文件位于/etc/mime.types.

在Windows下，该文件位于C:/apache/conf/mime.types（类似这样的，注意Apache的安装路径）。

该文件是一个一对多的映射表，定义了某一种文件类型，对应的几种后缀。

除了该文件，在Apache的配置文件中，还可以用AddCharset语句添加映射，如：



