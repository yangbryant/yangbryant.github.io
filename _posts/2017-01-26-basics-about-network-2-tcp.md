---
layout: post
title: 计算机网络基础(二) -- TCP协议
date: 2017-01-26 10:59:59 +08:00
tags: 温故而知新
---

***

#### 计算机网络基础:

[计算机网络基础(一) -- HTTP协议][http_basic]  
[计算机网络基础(二) -- TCP协议][tcp_basic]  
[计算机网络基础(三) -- IP协议][ip_basic]  
[计算机网络基础(四) -- Socket编程实例][socket_programming]  

***

### TCP 协议

* TCP 提供一种 **面向连接** 的, **可靠** 的 **字节流** 服务.
* TCP 连接中, 仅有两方进行通信. **广播,多播** 不能用于TCP.
* TCP 使用 **校验和**, **确认和重传** 机制保证可靠传输.
* TCP 使用 **累计确认**.
* TCP 使用 **滑动窗口** 机制来实现流量控制, 通过 **动态改变窗口大小** 进行拥塞控制

#### 三次握手

* 三次握手, Three-way Handshake, 建立TCP连接,需要客户端和服务器发送 **三个包**.
* 三次握手的目的.连接指定端口,建立TCP连接,同步连接双方的序列号和确认号, 交换TCP窗口的大小信息.
* 在 **Socket编程** 中,客户端执行 `connect()` 时,触发三次握手.

```
第一次握手 (SYN=1, seq=x):

	客户端发送一个TCP的包, SYN 标志位 为1. 设置服务器端口,初始序号 x, 保存在包头的序列号(Sequence Number)字段.
	发送完毕后, 客户端进入 `SYN_SEND` 状态.
	
第二次握手 (SYN=1, ACK=1, seq=y, ACKnum=x+1):

	服务器发回确认包(ACK)应答, SYN标志位 和 ACK标志位 均为1. 服务器端选择自己的 ISN 序列号, 放到 Seq域里.
	同时,将确认序号(Acknowledgement Number)设置为客户的 ISN 加 1 (x+1).
	发送完毕后,服务器端进入 `SYN_RCVD` 状态.
	
第三次握手 (ACK=1, ACKnum=y+1):

	客户端再次发送确认包(ACK), SYN 标志位 为0, ACK 标志位 为1. 把服务器发来的 ACK 的序号字段 加 1 (y+1).
	放在确定字段中发送给对方, 在数据段放写 ISN+1.
	发送完毕后,客户端进入 `ESTABLISHED` 状态.
	当服务器端接收到这个包时,也进入 `ESTABLISHED` 状态, TCP握手结束.
	
```

* 三次握手过程示意图:
![三次握手过程示意图][three_way_handshake]

#### 四次挥手

* 四次挥手, Four-way Handshake, 也叫改进的三次握手. 拆除TCP连接,需要发送 **四个包**.
* 客户端或者服务器都可主动发起挥手动作.
* 在 **Socket编程** 中,任何一方执行 `close()` 时,触发挥手操作.

```
第一次挥手 (FIN=1, seq=x):

	客户端关闭连接, 发送一个 FIN 标志位 为1 的包,表示没有数据要发送了,但可以接收数据.
	发送完毕后,客户端进入 `FIN_WAIT_1` 状态.	

第二次挥手 (ACK=1, ACKnum=x+1):

	服务器端确认客户端的 FIN 包,发送一个确认包, 表明接收了关闭连接的请求,但还没准备好关闭连接.
	发送完毕后,服务器进入 `CLOSE_WAIT` 状态.
	客户端接收到这个确认包, 进入 `FIN_WAIT_2` 等待服务器关闭连接.

第三次挥手 (FIN=1, seq=y):

	服务器准备好关闭连接时,向客户端发送结束连接请求, FIN 标志位 为1.
	发送完毕后,服务器进入 `LAST_ACK` 状态,等待来自客户端的的最后一个ACK.

第四次挥手 (ACK=1, ACKnum=y+1):

	客户端接收到关闭请求,发送一个确认包,进入 `TIME_WAIT` 状态,等待可能出现的要求重传的ACK包.
	服务器接收到确认包,关闭连接,进入 `CLOSED` 状态.
	客户端等待了某个固定的时间 (2*最大段生命周期, 2*MSL, 2 Maximum Segment Lifetime) 之后,
	没有收到服务器的ACK,认为服务器已正常关闭,自己也关闭连接,进入 `CLOSED` 状态.

```

* 四次挥手过程示意图:
![四次挥手过程示意图][four_way_handshake]

#### SYN攻击

* SYN 攻击 (SYN Flood)
1. 服务器发送 **SYN-ACK** 后,收到客户端发送的 **ACK** 之前的TCP连接称为 **半连接** (half-open connect).
2. 攻击客户端在短时间内伪造大量的不存在的IP地址,向服务器发送SYN包,服务器回复确认包,等待客户端的确认.
3. 由于源客户端 **不存在** ,服务器需要 **不断重发直至超时** .
4. 这些伪造的SYN包长时间占用 **未连接队列** ,正常的SYN请求被 **丢弃** ,导致目标系统运行缓慢,网络拥堵,甚至系统瘫痪.
5. SYN攻击是一种典型的 **DoS/DDoS攻击**.

* 如何检测SYN攻击: 在服务器上看到有大量的半连接状态时,特别IP地址是随机的,基本断定是一次SYN攻击. **netstats** 命令可以检测SYN攻击.
* 如何防御SYN攻击: 在TCP协议上分析,SYN攻击不能完全被阻止.只能尽可能减轻SYN攻击的危害.常用的防御方法:
1. 缩短超时(SYN Timeout)时间
2. 增加最大半连接数
3. 过滤网关防护
4. SYN Cookies技术

***

此文参考于 [hit-alibaba.github.io][hit-alibaba.github.io],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[hit-alibaba.github.io]: https://hit-alibaba.github.io/interview/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[http_basic]: /2017/01/basics-about-network-1-http/
[tcp_basic]: /2017/01/basics-about-network-2-tcp/
[ip_basic]: /2017/01/basics-about-network-3-ip/
[socket_programming]: /2017/01/basics-about-network-4-socket-programming/

[three_way_handshake]: /assets/images/network/tcp-connection-made-three-way-handshake.png 'three_way_handshake'
[four_way_handshake]: /assets/images/network/tcp-connection-closed-four-way-handshake.png 'four_way_handshake'