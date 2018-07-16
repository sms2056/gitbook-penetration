# XSS原理分析与解剖

![](/attackUsers/xss/image/xss-1.png)

---

## 01. 前言:

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

## 02. 原理:
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
我们输入`<script>alert(‘xss’)</script>`，得到的页面为
![](/attackUsers/xss/image/xss-5.png)
看来，我们的假设成功了，以上内容就是XSS的原理，下面我们来说说xss的构造和利用

## 03. xss利用输出的环境来构造代码:
上节说了xss的原理，但是我们的输出点不一在`<br>`和`</boby>`里，可以出现在html标签的属性里，或者其他标签里面。
所以这节很重要，因为不一定 当你输入`<script>alert(‘xss’)</script>`就会弹窗。

先贴出代码:


```html

<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8" /> 
        <title>XSS利用输出的环境来构造代码</title>
    </head>
    <body>
        <center>
            <h6>把我们输入的字符串 输出到input里的value属性里</h6>
            <form action="" method="get">
                <h6>请输入你想显现的字符串</h6>
                <input type="text" name="xss_input_value" value="输入"><br>
                <input type="submit">
            </form>
            <hr>
            <?php
                $xss = $_GET['xss_input_value'];
                if(isset($xss))
                {
                    echo '<input type="text" value="'.$xss.'">';
                }
                else
                {
                    echo '<input type="type" value="输出">';
                }
            ?>
        </center>
    </body>
</html>

```

下面是代码的页面
![](/attackUsers/xss/image/xss-6.png)

这段代码的作用是把第一个输入框的字符串，输出到第二个输入框，我们输入1，那么第二个input里的value值就是1，下面是页面的截图和源代码的截图(这里我输入`<script>alert(‘xss’)</script>`来测试)
![](/attackUsers/xss/image/xss-7.png)
点击提交
![](/attackUsers/xss/image/xss-8.png)

明显的可以看到，并没有弹出对话框，大家可能会疑惑为什么没有弹窗呢，我们来看看源代码
![](/attackUsers/xss/image/xss-9.png)

我们看到我们输入的字符串被输出到第15行input标签里的value属性里面，被当成value里的值来显现出来，所以并没有弹窗，这时候我们该怎么办呢？聪明的人已经发现了可以在`<script>alert(&#039;xss&#039;)</script>`前面加个`”>`来闭合input标签。所以应该得到的结果为
![](/attackUsers/xss/image/xss-10.png)

成功弹窗了，我们在看看这时的页面
![](/attackUsers/xss/image/xss-11.png)

看到后面有第二个input输入框后面跟有”>字符串，为什么会这样呢，我们来看看源代码
![](/attackUsers/xss/image/xss-12.png)

这时可以看到我们构造的代码里面有两个`”>`，第一个`”>`是为了闭合input标签，所以第二个`”>`就被抛弃了，因为html的容错性高，所以并没有像php那样出现错误，而是直接把多余的字符串来输出了，有的人是个完美主义者，不喜欢有多余的字符串被输出，这时该怎么办呢？

这里我问大家一个问题，我之前说的xss代码里，为什么全是带有标签的。难道就不能不带标签么？！答：当然可以。既然可以不用标签，那我们就用标签里的属性来构造XSS，这样的话，xss代码又少，又不会有多余的字符串被输出来。

还是这个环境，但是不能使用标签，你应该怎么做:想想input里有什么属性可以调用js，html学的好的人，应该知道了，on事件，对的。我们可以用on事件来进行弹窗，比如这个xss代码 我们可以写成
>`” onclick=”alert(&#039;xss&#039;)`

这时，我们在来试试，页面会发生什么样的变化吧。
![](/attackUsers/xss/image/xss-13.png)

没有看到弹窗啊，失败了么？答案当然是错误的，因为onclick是鼠标点击事件，也就是说当你的鼠标点击第二个input输入框的时候，就会触发`onclick事件`，然后执行`alert(&#039;xss&#039;)`代码。我们来试试看
![](/attackUsers/xss/image/xss-14.png)

当我点击后，就出现了弹窗，这时我们来看看源代码吧