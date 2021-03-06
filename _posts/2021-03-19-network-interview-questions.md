---
title:  "网络面试题"
date:   2021-03-18 11:09:23
categories: [面试]
tags: [network]
---

## 网络面试题

1、谈一谈对TCP/IP四层模型，OSI七层模型的理解？

#### OSI七层概念

- 物理层：通过网线、光缆等这种物理方式将设备连接起来；
- 数据链路层：建立逻辑连接，进行硬件地址寻址，差错校验等功能；
- 网络层：进行逻辑地址寻址，实现不同网络之间的路径选择；
- 传输层：通过分级、流量控制和差错控制来控制通信的可靠性；
- 会话层：建立和管理连接，启用、发送和接收数据，会话层有自己的API或应用程序接口。NetBIOS(网络基本输入输出系统)；
- 表示层：从应用层接收数据，这些数据以字符和数字的形式出现的，表示层将这些数字和字符转换成机器可理解的二进制格式；
- 应用层：包含应用层协议，从而使应用程序在网络中正常运行；

#### TCP/IP四层模型

- 数据链路层：也叫网络访问层、网络接口层；他包含了OSI模型的物理层和数据链路层，把电脑连接起来；
- 网络层：也叫IP层，处理IP数据包的传输、路由，建立主机间的通讯；
- 传输层：为两台主机提供端到端的通信；
- 应用层：包含OSI的会话层、表示层和应用层，提供一些常用的协议规范，如：FTP、SMPT、HTTP等；

2、说说TCP 3次握手的过程？

- client端建立连接，发送SYN同步包，发送之后状态变成SYN_SENT;
- server端收到SYN之后，同意建立连接，返回一个ACK响应，同时也会给client发送一个SYN包，发送完成之后状态变为SYN_RCVD
- client端收到server的ACK之后，状态变成ESTABLISHED，返回ACK给server端。server收到之后状态也编委ESTABLISHED

3、为什么要3次？2次，4次不行吗？

- 因为TCP是双工传输模式，不区分客户端和服务端，连接建立是双向的过程；
- 如果只有两次，无法做到双向连接的建立，从建立连接server回复的SYN和ACK合并成一次可以看出来，不需要4次。

4、4次挥手的过程呢？

- client端向server发送FIN包，进入FIN_WAIT_1状态，这代表client端没有数据要发送了；
- server端收到之后，返回一个ACK，进入CLOSE_WAIT等待关闭的状态，因为server端可能还有没有发送完成的数据；
- 等到server端数据都发送完毕之后，server端就向client发送FIN，进入LAST_ACK状态；
- client收到FIN之后，进入TIME_WAIT的状态，同时回复ACK，server收到之后直接进入CLOSED状态，连接关闭。但是client要等待2MSL(报文最大生存时间)的时间，才会进入CLOSED状态。

5、为什么要等待2MSL的时间才关闭？

- 为了保证连接的可靠关闭，如果server没有收到最后一个ACK，那么会重发FIN
- 为了避免端口重用带来的数据混淆。如果client直接进入CLOSED状态，又用相同端口号向server建立一个连接，上一次连接的部分数据在网络中延迟到达server，数据就可能发生混淆了。

6、TCP怎么保证传输过程的可靠性？

- 校验和：发送方在发送数据之前计算校验和，接收方收到数据后同样计算，进行比对如果不一致，则传输有误；
- 确认应答，序列号:TCP进行传输时数据都进行了编号，每次接收方返回ACK都有确认序列号；
- 超时重传：如果发送方发送数据一段时间后没有收到ACK，那么就重发数据；
- 连接管理：三次握手与四次挥手的过程
- 流量控制：
- 拥塞控制：

7、说一下浏览器请求一个网址的过程？

- 首先通过DNS服务器把域名解析成IP地址，通过IP和子网掩码判断是否属于同一个子网；
- 构造应用层请求http报文，传输层添加TCP/UDP头部，网络层添加IP头部，数据链路层添加以太网协议头部；
- 数据经过路由器、交换机转发，最终达到目标服务器，目标服务器同样解析数据，最终拿到http报文，按照对应的程序的逻辑响应回去；

8、HTTPS的工作原理？

- 用户通过浏览器请求https网站，服务器收到请求，选择浏览器支持的加密和hash算法，同时返回数字证书给浏览器，包含颁发机构、网址公钥、证书有效期等信息；
- 浏览器对证书的内容进行校验，如有问题，则会提示警告，否则就生成一个随机数X，同时使证书中的公钥进行加密，并且发送给服务器；
- 服务器收到以后，使用私要解密，得到随机数X，然后使用X对网页内容进行加密，返回给服务器；
- 浏览器则使用X与之前约定的加密算法进行解密，得到最终的网页内容；

9、负载均衡有哪些实现方式？

- DNS：这是最简单的负载均衡的方式，一般用于实现地理级别的负载均衡，不同地域的用户通过DNS的解析可以返回不同的IP地址，这种方式的负载均衡简单，但是扩展性太差，控制权在域名服务商；
- Http重定向：通过修改Http响应头的Location达到负载均衡的目的，Http的302重定向。这种方式对性能有影响，而且增加请求耗时；
- 反向代理：作用于应用层的模式，也被称作为七层负载均衡，比如常见的Nginx，性能一般可以达到万级。这种方式部署简单，成本低，而且容易扩展；
- IP：作用于网络层的和传输层的模式，也被称作四层负载均衡，通过对数据包的IP地址和端口进行修改来达到负载均衡的效果。常见的有LVS（Linux Virtual Server），通常性能可以支持10万级并发；

10、http协议和https协议的不同和其大概原理？

#### http与https区别

- https协议需要到ca申请证书，一般免费证书较少，因而需要一定费用;
- http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议;
- http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443；
- http的连接是无状态的；https是由http+ssl协议构建的可进行加密传输、身份认证的网络协议，比http安全；

## 未完待续
