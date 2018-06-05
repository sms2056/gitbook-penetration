# tomcat安装配置

![](/weakPassword/image/tomcat-logo.png)

---

## 一. 实验环境

| 软件名称 | 软件版本 |
| :--- | :--- |
| Windows | windows server 2008 |
| JDK | jdk-7u80-windows-i586 |
| tomcat | 4~9都可以 |

## 二. 安装步骤

### I.JDK安装与环境配置

a\). 将下载的`jdk-7u80-windows-i586.exe`进行安装,直接下一步,下一步就OK

b\). 默认安装在`C:\Program Files\Java\`文件夹下

c\). 设置java环境变量

> 我的电脑 --&gt; 右键 --&gt; 属性 --&gt; 高级系统设置 --&gt; 环境变量

```
JAVA_HOME：C:\Program Files\Java\jdk;      //jdk安装路径，到bin上一层。
CLASSPATH：%JAVA_HOME%\lib\tools.jar;
Path：%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
```

> 在cmd下运行`“Java”、“javac”、“Java -version”`，如果安装成功，则不会出现错误提示信息

![](/weakPassword/image/tomcat_1.png)

### II.tomcat安装

a\). 下载tomcat后解压到自己想要安装的位置

b\). 同Java环境变量的配置，新建以下四个变量（已存在则进行编辑）：

```
TOMCAT_HOME    ：C:\Program Files\tomcat　　//tomcat安装路径，到bin上一层。
CATALINA_HOME  ：C:\Program Files\tomcat　　//同上
Path：          %CATALINA_HOME%\bin        //修改PATH
CLASSPATH：     %CATALINA_HOME%\lib\servlet-api.jar    //修改CLASSPATH
```

c\).**运行方式一**:服务方式

在cmd下cd到C:\Program Files\tomcat\bin，运行“service install Tomcat9”命令即可。

在浏览器地址栏输入“http://localhost:8080”出现以下画面即使安装成功了!![](/weakPassword/image/tomcat_3.png)

进入到comcat的解压目录`(C:\Tomcat\apache-tomcat-5\bin)`双击运行`startup.bat`

![](/weakPassword/image/tomcat_2.png)

## 三. 配置说明

**所有配置文件全部都在tomcat路径的conf文件夹中**

**a\). 端口设置\(server.xml\)**

```xml
<Connector 
    port="8080"               //端口设置
    maxHttpHeaderSize="8192"
    maxThreads="150"
    minSpareThreads="25"
    maxSpareThreads="75"
    enableLookups="false"
    redirectPort="8443"
    acceptCount="100"
    connectionTimeout="20000"
    disableUploadTimeout="true"
/>
```

**b\). Manager权限设置\(tomcat-users.xml\)**

Tomcat Manager是Tomcat自带的、用于对Tomcat自身以及部署在Tomcat上的应用进行管理的web应用。

在默认情况下，Tomcat Manager是处于禁用状态的。准确地说，Tomcat Manager需要以用户角色进行登录并授权才能使用相应的功能，不过Tomcat并没有配置任何默认的用户，因此需要我们进行相应的用户配置之后才能使用Tomcat Manager。

Tomcat Manager的用户配置是在`Tomcat安装目录/conf/tomcat-users.xml`文件中进行管理的。

只需要在`tomcat-users`节点中配置相应的`role`\(角色/权限\)和`user`\(用户\)即可。

一个`user`节点表示单个用户，属性`username`和`password`分别表示登录的用户名和密码，属性`roles`表示该用户所具备的权限。`user`节点的`roles`属性值与`role`节点的`rolename`属性值相对应，表示当前用户具备该role节点所表示的角色权限。当然，一个用户可以具备多种权限，因此属性`roles`的值可以是多个`rolename`，多个`rolename`

稍加思考，我们就应该猜测到，`rolename`的属性值并不是随意的内容，否则Tomcat怎么能够知道我们随便定义的`rolename`表示什么样的权限呢。实际上，Tomcat已经为我们定义了4种不同的角色——也就是4个`rolename`，我们只需要使用Tomcat为我们定义的这几种角色就足够满足我们的工作需要了。之间以英文逗号隔开即可。

| 用户权限 | 说明 |
| :--- | :--- |
| admin-gui | 允许访问HTML GUI，可以避免CSRF攻击 |
| admin-script | 允许访问文本接口 |
| manager-gui | 允许访问html接口\(即URL路径为/manager/html/\*\) |
| manager-script | 允许访问纯文本接口\(即URL路径为/manager/text/\*\) |
| manager-jmx | 允许访问JMX代理接口\(即URL路径为/manager/jmxproxy/\*\) |
| manager-status | 允许访问Tomcat只读状态页面\(即URL路径为/manager/status/\*\) |

Tomcat Manager内部配置文件中可以得知，manager-gui、manager-script、manager-jmx均具备manager-status的权限，也就是说，manager-gui、manager-script、manager-jmx三种角色权限无需再额外添加manager-status权限，即可直接访问路径/manager/status/\*

```
<tomcat-users>
  //add
  <role rolename="manager-status"/>
  <role rolename="manager-jmx"/>
  <role rolename="manager-script"/>
  <role rolename="manager-gui"/>
  //==========================以下默认存在
  <role rolename="tomcat"/>
  <role rolename="role1"/>
  <user username="role1" password="tomcat" roles="role1"/>
  <user username="both" password="tomcat" roles="tomcat,role1"/>
  <user username="tomcat" password="tomcat" roles="tomcat"/>
  //==========================以上默认存在
  //add
  <user username="admin" password="admin" roles="manager-status"/>
  <user username="admin" password="admin" roles="manager-gui,manager-script"/>
  <user username="admin" password="admin" roles="manager-jmx"/>
</tomcat-users>
```

c\). 上传文件大小设置\(server.xml\)

tomcat目录下的conf文件夹下，server.xml 文件中以下的位置中添加maxPostSize参数

```html
<Connector port="8081"    
               maxThreads="150" minSpareThreads="25" maxSpareThreads="75"    
               enableLookups="false" redirectPort="8443" acceptCount="100"    
               debug="0" connectionTimeout="20000"     
               disableUploadTimeout="true" URIEncoding="utf-8"    
               maxPostSize="0"/>    
```



