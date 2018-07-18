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
