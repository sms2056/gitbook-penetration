# Apache安全加固

---

## 一. httpd.conf配置文件说明

##### 1. ServerRoot

![](/fileParser/image/apache-conf-1.jpg)

主要用于指定Apache的安装路径，此选项参数值在安装Apache时系统会自动把Apache的路径写入。Windows安装时，该选项的值为Windows安装的路径，Linux安装时该选项值为编译时选择的路径】

##### 2. Mutex default

![](/fileParser/image/apache-conf-2.jpg)

互斥：允许你为多个不同的互斥对象设置互斥机制【mutex mechanism】和互斥文件目录，或者修改全局默认值

如果互斥对象是基于文件的以及默认的互斥文件目录不在本地磁盘或因为其它原因而不适用，那么取消注释并改变目录。

##### 3. Listen

![](/fileParser/image/apache-conf-3.jpg)

主要侦听web服务端口状态，默认为：80，即侦听所有的地址的80端口，注意这里也可以写成IP地址的侦听形式，不写即默认的地址：0.0.0.0

##### 4. Dynamic Shared Object \(DSO\) Support\(动态共享对象支持\)

![](/fileParser/image/apache-conf-4.jpg)

主要用于添加Apache一些动态模块，比如php支持模块。重定向模块，认证模块支持，注意如果需要添加某些模块支持，只需把相关模块前面注释符号取消掉。如图所示，要对Apache添加某个功能模块，把前面的注释符号去掉就行

##### 5. Apache运行用户配置

![](/fileParser/image/apache-conf-5.jpg)

此选项主要用指定Apache服务的运行用户和用户组，默认为：daemon

##### 6. Apache服务默认管理员地址设置

![](/fileParser/image/apache-conf-6.jpg)

此选项主要用指定Apache服务管理员通知邮箱地址，选择默认值即可，如果有真实的邮箱地址也可以设置此值

##### 7. Apache的默认服务名及端口设置

![](/fileParser/image/apache-conf-7.jpg)

此选项主要用指定Apache默认的服务器名以及端口，默认参数值设置为：ServerName localhost:80

##### 8. Apache的根目录访问控制设置

![](/fileParser/image/apache-conf-8.jpg)

此选项主要是针对用户对根目录下所有的访问权限控制，默认Apache对根目录访问都是拒绝访问。

##### 9. Apache的默认网站根目录设置及访问控制

![](/fileParser/image/apache-conf-9.jpg)

此区域的配置文件，主要是针对Apache默认网站根目录的设置以及相关的权限访问设置，默认对网站的根目录具有访问权限，此选项默认值即可

##### 10. Apache的默认首页设置

![](/fileParser/image/apache-conf-10.jpg)

此区域文件主要设置Apache默认支持的首页，默认只支持:index.html首页，如要支持其他类型的首页，需要在此区域添加:如index.php表示支持index.php类型首页

##### 11. Apache关于.ht文件访问配置

![](/fileParser/image/apache-conf-11.jpg)

此选项主要是针对.ht文件访问控制，默认为具有访问权限，此区域文件默认即可

##### 12. Apache关于日志文件配置

![](/fileParser/image/apache-conf-12.jpg)

此区域文件主要是针对Apache默认的日志级别，默认的访问日志路径，默认的错误日志路径等相关设置，此选项内容默认即可

##### 13. URL重定向，cgi模块配置说明

![](/fileParser/image/apache-conf-13.jpg)

![](/fileParser/image/apache-conf-14.jpg)

![](/fileParser/image/apache-conf-15.jpg)

此区域文件主要包含一些URL重定向，别名，脚本别名等相关设置，以及一些特定的处理程序，比如cgi设置说明

##### 14. MIME媒体文件，以及相关http文件解析配置说明

![](/fileParser/image/apache-conf-16.jpg)

此区域文件主要包含一些mime文件支持，以及添加一些指令在给定的文件扩展名与特定的内容类型之间建立映射关系，比如添加对php文件扩展名映射关系

##### 15. 服务器页面提示设置

![](/fileParser/image/apache-conf-17.jpg)

此区域可定制的访问错误响应提示，支持三种方式：1明文 ，2本地重定向 3，外部重定向；另外还包括内存映射或“发送文件系统调用”可被用于分发文件等配置

##### 16. Apache服务器补充设置

![](/fileParser/image/apache-conf-18.jpg)

此区域主要包括：服务器池管理，多语言错误消息，动态目录列表形式配置，语言设置，用户家庭目录，请求和配置上的实时信息，虚拟主机，Apache Http Server手册，分布式创作和版本控制，多种类默认设置，mod\_proxy\_html，使其支持HTML4/XHTML1等等补充配置的补充

##### 17. Apache服务器安全连接设置

![](/fileParser/image/apache-conf-19.jpg)

此区域主要是关于服务器安全连接设置，用于使用https连接服务器等设置的地方

## 二. 重要安全项

```
# 用于设置页面信息,On|Off
ServerSignature On
# 用于设置服务器字段的信息, Minimal|ProductOnly|OS|Full 
ServerTokens Full
```

![](/fileParser/image/apache-sec_1.png)

