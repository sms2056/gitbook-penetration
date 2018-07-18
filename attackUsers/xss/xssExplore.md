# 不常见的xss利用探索

---

## 01. 前言
反射型xss，相对于持久型xss来说比较鸡肋；需要欺骗用户点击构造好的链接，达到窃取cookie，或是进一步CSRF劫持用户操作的目的。若是get型的xss，javascript代码直接在url中，虽然有些怪异，也好歹能用，愿者上钩。但是若js代码是在post数据包，或者是在header里，那就更显得鸡肋了，甚至无法利用。我查阅了大量的资料，有了下面的尝试。

## 02. POST型反射xss
对于post反射型xss，其实已经有比较成熟的利用方法：构造post表单，利用js直接提交。
表单构造如下：

![](/attackUsers/xss/image/xss-38.png)

将html保存后，诱导被攻击者访问这个html文件，触发如下：

![](/attackUsers/xss/image/xss-39.png)

## 03. http头部反射xss
如果xss代码的输入点是在http头部的话，那么利用表单提交的方法就不行。利用ajax异步跨域请求的方法等会再谈，先说说一个比较特殊的头信息referer。

#### referer头信息xss
当浏览器进行跳转时，一般会将前一个页面的url带入referer头部中，如果我们控制了跳转前的url，并使之跳转到target页面，那么referer头的xss漏洞便可以利用。当然，chrome和firefox会对跳转前url里的”<>”等进行urlencode，但是IE却不会，所以这种方法在IE下适用。
漏洞页面如下：

![](/attackUsers/xss/image/xss-40.png)

简单的将referer信息输出，那么构造一个跳转：

![](/attackUsers/xss/image/xss-41.png)

访问在IE11上测试成功

![](/attackUsers/xss/image/xss-42.png)

除了window.location跳转外，还可以利用iframe、表单提交等方式。

>利用iframe标签:

![](/attackUsers/xss/image/xss-43.png)

>利用表单提交的方式:

![](/attackUsers/xss/image/xss-44.png)

#### 对其他header的xss尝试
如何让受害者点击某个链接后，访问漏洞页面并带上特定的header信息，ajax可以办到这点。由于需要跨域请求，这里参考了CORS(Cross Origin Resourse-Sharing)的模型。CORS模型实现跨域资源共享需要服务器端设置一定的返回头部，所以这里攻击场景就比较狭隘，仅做学术的研究。服务器端可设置的http头如下：

> ** Access-Control-Allow-Origin: ** 允许跨域访问的域，可以是一个域的列表，也可以是通配符”*”。这里要注意Origin规则只对域名有效，并不会对子目录有效。即http://foo.example/subdir/是无效的。但是不同子域名需要分开设置，这里的规则可以参照那篇同源策略

> Access-Control-Allow-Credentials: 是否允许请求带有验证信息，这部分将会在下面详细解释

> Access-Control-Expose-Headers: 允许脚本访问的返回头，请求成功后，脚本可以在XMLHttpRequest中访问这些头的信息(貌似webkit没有实现这个)

> Access-Control-Max-Age: 缓存此次请求的秒数。在这个时间范围内，所有同类型的请求都将不再发送预检请求而是直接使用此次返回的头作为判断依据，非常有用，大幅优化请求次数

> Access-Control-Allow-Methods: 允许使用的请求方法，以逗号隔开