# 图解HTTP

![](/base/image/httpProtocol/httpP-logo.jpg)

---

## 0x01 TCP/IP

了解HTTP，就必须要了解些TCP/IP协议族。其实机器是很笨的，没有任何智商，想让机器之间通信，必须要有一套完整通信规则，包括如何到达通信目标，使用的传输类型，等等都需要一套规范，这一整套规范就是协议。  
TCP/IP协议就是其中的这么一套协议族。

#### TCP/IP 协议的四层分层管理

| id | 分层 | 解释 |
| :--- | :--- | --- |
| 1. | 应用层 | 具体的协议有，HTTP，FTP（文件传输协议），DNS（域名系统，就是IP与域名互相转换）等。从这里可以看出HTTP协议是TCP/IP协议族的组成部分 |
| 2. | 传输层 | TCP（面向连接的，传输控制协议），UDP（面向无连接的，用户数据报协议） |
| 3. | 网络层 | IP协议 |
| 4. | 数据链路层 | 处理网络的硬件部分，比如，网卡，通信线路（光纤，电缆） |

![](/base/image/httpProtocol/httpP-1.png)

如图所示的，这是整个通信过程中数据流的走向，同时经过每层协议时，都会加上相应的首部，每个首部都会有相应的功能。 
各个分层之间相互协作，当一个部分出现问题，可以找到相应的层次来处理问题，而不是把整个协议都去检查一遍。 

## 0x02 简单的HTTP协议

HTTP协议一般是用户客户端和服务器之间的通信，一般都是客户端请求，服务端响应，类似于一问一答式的通信。

![](/base/image/httpProtocol/httpP-2.png)

就问你，这张图是不是好丑。哈哈，好吧，图虽然丑了点，但是不影响这表达的意思嘛，就是在两台机器通信时使用HTTP协议中，肯定有一方是客户端，一方是服务器。 

![](/base/image/httpProtocol/httpP-3.png)

这就是HTTP通信最基本的格式了，从1中发送请求，从2中返回响应。
请求的格式是：

```
GET /index.htm HTTP/1.1
Host: baidu.com

########################################################
# GET        : 表示的是访问服务器的类型，或者叫请求方法
# /index.htm : 指明了请求访问的资源对象，也叫请求URI
# HTTP/1.1   : 表示HTTP的版本号，也是提示客户端使用的HTTP协议功能
```

请求报文是由请求方法，请求URI，协议版本，可选的请求首部字段和内容实体构成。 
** 请求报文格式 : **

![](/base/image/httpProtocol/httpP-4.png)

** 响应报文格式 : **

![](/base/image/httpProtocol/httpP-5.png)

## 0x03 HTTP是无状态的协议
在通信过程中，HTTP协议本身不对`请求`和`响应`的*通信状态*进行保存。 
当新的请求发送时，并不知道上次是否已经发送类似的请求。举个例子，就是当我们登陆到一个购物网站时，当跳转到其他页面时，发出的请求也应该是保持登陆的状态才能有更好的购物体验，不可能说每打开一个页面再登陆一次，这样肯定很麻烦。为了实现这种保持状态的功能，引入了Cookie功能。具体做法是在服务端引入Session功能，在服务端用Session来管理状态，生成SessionID，传给客户端，客户端将SessionID保存在Cookie中，每次请求时在Cookie中取出SessionID放入到HTTP请求中，这样服务器通过SessionID知道你以前的请求状态，是否登录过等信息。

## 0x04 HTTP首部

### 4.1 HTTP请求报文
请求报文中，HTTP报文有方法，URI，HTTP版本，HTTP首部字段等构成。

![](/base/image/httpProtocol/httpP-6.png)

### 4.2 HTTP响应报文
响应报文中，HTTP报文由HTTP版本，状态码，HTTP首部字段构成。

![](/base/image/httpProtocol/httpP-7.png)

### 4.3 HTTP首部字段
HTTP首部字段结构 :
> 首部字段名：字段值 

```
#例:
Host         :baidu.com
Cache-Control: max-age = 0
Content-Type : text/html
```

> 有的字段值可以是多个值

```
#例:
Keep-Alive   : timeout = 15,max = 100
```

### 4.4 HTTP状态码
响应报文中HTTP状态码表示了客户端HTTP请求的返回结果，得到响应的结果码，有助于了解我们请求时是否成功，如果错误了，是哪种类型的错误。状态码有5种类型，分别表示了对应的返回响应原因。 

![](/base/image/httpProtocol/httpP-8.png)

> 2XX，表示响应结果为请求被正常处理

|状态码|解释|
|----|----|
|200 OK|表示客户端发来的请求在服务端被正常处理|
|204 NO Content|表示处理成功，但是响应中没有任何实体|
|206 Partial Content |表示客户端通过发送范围请求头Range抓取到了资源的部分数据|

> 3XX，表示浏览器需要执行某些特殊的处理以正确处理请求

|状态码|解释|
|----|----|
|301 Moved Permanently |永久性重定向，表示该资源已经分配了新的URI，原来URI不再使用|
|302 Found |临时性重定向，跟301类似，请求的资源分配了新的URI，但只是临时的，以后可能会换回来|
|304 Not Modified|表示服务器允许请求访问资源，但是没有满足条件。比如你客户端需要请求一个信息，但是要求该信息是今天编辑的，但是资源里确实有该信息，只是这个信息是昨天编辑的，不符合条件，则服务器返回该状态码。|

> 4XX，表示客户端是发生错误的原因所在

|状态码|解释|
|----|----|
|400 Bad Request |表示请求报文中存在语法错误|
|401 Unauthorized|没有认证，表示发送的请求组要进行HTTP认证|
|403 Forbidden|表示不允许访问这个资源|
|404 Not Found|表示服务器无法找到请求的资源|

> 5XX 表示服务器本身发生错误

|状态码|解释|
|----|----|
|500 Internal Server Error|表示服务器在执行时发生错误|
|503 Service unavailable|表示服务器暂时处于超负荷或者正在停机维护，无法处理请求|

### 4.5 HTTP通用首部字段
通用首部字段是指，请求报文和响应报文双方都会使用的首部

![](/base/image/httpProtocol/httpP-9.png)

##### Cache-Control:

> 通过指定首部字段 Cache-Control 的指令，就能操作缓存的工作机制

![](/base/image/httpProtocol/httpP-13.png)

首部字段 Cache-Control 的指令可用于请求及响应时。

```
# 指令的参数是可选的，多个指令之间通过“,”分隔。
Cache-Control: private, max-age=0, no-cache​
```
** 缓存请求指令 **

** 缓存响应指令 **



### 4.6 请求首部字段

![](/base/image/httpProtocol/httpP-10.png)

### 4.7 响应首部字段

![](/base/image/httpProtocol/httpP-11.png)

### 4.8 实体首部资源

![](/base/image/httpProtocol/httpP-12.png)

### 4.9 非HTTP/1.1首部字段
还有一些并不是HTTP协议本身的字段，但是很有用，也需要了解。比如为Cookie服务的首部字段，有Set-Cookie（响应首部字段） 和Cookie（请求首部字段），这是管理服务器和客户端之间状态的信息，它的工作机制是用户识别和状态管理。
这些非正式的首部字段统一归纳在 RFC4229 HTTP Header Field Registrations 中
















































