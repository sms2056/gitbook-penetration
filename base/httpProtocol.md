# 图解HTTP

---
## 0x01 TCP/IP与HTTP
了解HTTP，就必须要了解些TCP/IP协议族。其实机器是很笨的，没有任何智商，想让机器之间通信，必须要有一套完整通信规则，包括如何到达通信目标，使用的传输类型，等等都需要一套规范，这一整套规范就是协议。
TCP/IP协议就是其中的这么一套协议族。

#### TCP/IP 协议的四层分层管理

1、应用层 : 具体的协议有，HTTP，FTP（文件传输协议），DNS（域名系统，就是IP与域名互相转换）等。从这里可以看出HTTP协议是TCP/IP协议族的组成部分。 
2、传输层 : TCP（面向连接的，传输控制协议），UDP（面向无连接的，用户数据报协议）。 
3、网络层 : IP协议。 
4、数据链路层 : 处理网络的硬件部分，比如，网卡，通信线路（光纤，电缆）。