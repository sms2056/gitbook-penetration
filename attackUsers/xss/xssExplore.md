# 不常见的xss利用探索

---

## 01 前言
反射型xss，相对于持久型xss来说比较鸡肋；需要欺骗用户点击构造好的链接，达到窃取cookie，或是进一步CSRF劫持用户操作的目的。若是get型的xss，javascript代码直接在url中，虽然有些怪异，也好歹能用，愿者上钩。但是若js代码是在post数据包，或者是在header里，那就更显得鸡肋了，甚至无法利用。我查阅了大量的资料，有了下面的尝试。

## 02 POST型反射xss
对于post反射型xss，其实已经有比较成熟的利用方法：构造post表单，利用js直接提交。
表单构造如下：

![](/attackUsers/xss/image/xss-38.png)

将html保存后，诱导被攻击者访问这个html文件，触发如下：

![](/attackUsers/xss/image/xss-39.png)

## 03 http头部反射xss