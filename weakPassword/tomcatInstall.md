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

在浏览器地址栏输入“[http://localhost:8080”出现以下画面即使安装成功了!\[\]\(/weakPassword/image/tomcat\_3.png\)d\](http://localhost:8080”出现以下画面即使安装成功了![]%28/weakPassword/image/tomcat_3.png%29d%29%29. **运行方式二**: 批处理命令%28推荐\)

进入到comcat的解压目录`(C:\Tomcat\apache-tomcat-5\bin)`双击运行`startup.bat`

![](/weakPassword/image/tomcat_2.png)

## 三. 配置说明

**所有配置文件全部都在tomcat路径的conf文件夹中**

a\). 端口设置\(server.xml\)

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

b\). Manager权限设置\(tomcat-users.xml\)

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
  <user username="admin" password="admin" roles="manager-gui,manager-script,manager-jmx,manager-status"/>
</tomcat-users>
```

c\). 上传文件大小设置

