# XSS编码与绕过

![](/attackUsers/xss/image/xss-17.png)

---

## 01. URL编码
URL只允许用US-ASCII字符集中可打印的字符(0×20—0x7x)，其中某些字符在HTTP协议里有特殊的意义，所以有些也不能使用。这里有个需要注意的，+加号代表URL编码的空格，%20也是。

URL编码最长见的是在用GET/POST传输时，顺序是把字符改成%+ASCII两位十六进制(先把字符串转成ASCII编码，然后再转成十六进制)。

这个在编码DOM XSS可能会用到，比如我上次说的`<<XSS分类与漏洞挖掘>>`0×04节DOM XSS 在chrome maxthon firefox里对URL里的a=后面的字节进行了URL进行编码，只能在360浏览器里成功，这就是浏览器对URL进行了URL编码。

## 02. unicode编码
