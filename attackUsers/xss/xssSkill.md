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




