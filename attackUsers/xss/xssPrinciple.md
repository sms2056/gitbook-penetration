# XSS原理分析与解剖

![](/attackUsers/xss/image/xss-1.png)

---

## 01 前言:

xss是一门又热门又不太受重视的Web攻击手法，为什么会这样呢，原因有下：

```
1、耗时间
2、有一定几率不成功
3、没有相应的软件来完成自动化攻击
4、前期需要基本的html、js功底，后期需要扎实的html、js、actionscript2/3.0等语言的功底
5、是一种被动的攻击手法
6、对website有http-only、crossdomian.xml没有用
```
但是这些并没有影响黑客对此漏洞的偏爱，原因不需要多，只需要一个
xss几乎每个网站都存在，google、baidu、360等都存在。

## 02 原理:
首先我们现在本地搭建个PHP环境(可以使用phpstudy安装包安装)，然后在index.php文件里写入如下代码:

```html
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8" /> 
        <title>XSS原理重现</title>
    </head>
    <body>
        <form action="" method="get">
        <input type="text" name="xss_input">
        <input type="submit">
        </form>
        <hr>
        
        <?php
            $xss = $_GET['xss_input'];
            echo '你输入的字符为<br>'.$xss;
        ?>
    </body>
</html>
```
然后，你会在页面看到这样的页面
![](/attackUsers/xss/image/xss-2.png)

我们试着输入abcd123，得到的结果为
![](/attackUsers/xss/image/xss-3.png)

我们在看看源代码
![](/attackUsers/xss/image/xss-4.png)

我们输入的字符串被原封不动的输出来了.

>那这里我们提出来一个假设，假设我们在搜索框输入`<script>alert(‘xss’)</script>`会出现什么呢？
如果按照上面的例子来说，它应该在第12行的`<br>`与`</boby>`之间，变成`<br><script>alert(‘xss’)</script></boby>`，而这样,则会弹出一个对话框.

既然假设提出来，那我们来实现下这个假设成不成立吧。
我们输入<script>alert(‘xss’)</script>，得到的页面为
