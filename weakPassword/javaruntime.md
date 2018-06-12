# java运行环境配置

![](/weakPassword/image/jdk.jpg)

---

## JDK安装与环境配置

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




