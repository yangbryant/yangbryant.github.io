---
layout: post
title: 一些关于计算机网络的基础知识
date: 2017-01-26 10:58:59 +08:00
tags: 温故而知新
---

***

### 一. HTTP 协议

* HTTP构建于TCP/IP协议之上,默认端口号是 **80**.
* HTTP是 **无连接无状态** 的.

#### 请求报文

* HTTP协议以 **ASCII码** 传输.
* HTTP请求分 **状态行**, **请求头**, **消息主体** 三部分.

```
<method> <request-URL> <version>
<headers>

<entity-body>
```

* HTTP中 **POST** **DELETE** **PUT** **GET** 对应 增 删 改 查 4个操作, POST和GET区别详情点击[这里][Get_Post_Diff].
* GET 表示 **获取信息**, 数据内容在 **URL** 里, 协议对长度无限制, 浏览器和服务器对它限制.
* POST 表示 **修改资源**, 数据内容 *必须* 在 **HTTP包的包体** 里, 协议对长度无限制, 出于安全考虑,服务器对它做一定限制.
* 服务器通常根据 请求头(headers) **Content-Type** 字段获取请求的消息主体的编码方式.
 
#### 响应报文

* HTTP响应分 **状态行**, **响应头(Response Header)**, **响应正文** 三部分.
* 状态行由 **协议版本**, **数字形式的状态代码**, **状态描述** 三部分.
* 状态码如下:

|:--- |:--- |:--- |
| 200 | OK | 客户端请求成功 |
| 301 | Moved Permanently | 请求永久重定向 |
| 302 | Moved Temporarily | 请求临时重定向 |
| 304 | Not Modified | 文件未修改,可以直接使用缓存的文件 |
| 400 | Bad Request | 由于客户端请求有语法错误,不能被服务器所理解 |
| 401 | Unauthorized | 请求未经授权. 这个状态码必须和www-authenticate报头域一起使用 |
| 403 | Forbidden | 服务器收到请求,但是拒绝提供服务.服务器通常会在响应正文中给出不提供 服务的原因 |
| 404 | Not Found | 请求资源不存在 | 
| 500 | Internal Server Error | 服务器发生不可预期的错误,导致无法完成客户端的请求 |
| 503 | Service Unavailable | 服务器当前不能处理客户端的请求,在一段时间后,服务器可能会恢复正常 |


#### 条件Get

* 客户端向服务器发送包询问在上一次访问后页面是否发生更改,如果没有,则通知使用本地缓存,如果有更新,则更新网页给用户.
* 流程如下:
1. 第一次请求:服务器返回请求数据.
2. 第二次请求:请求中添加 **If-Modified-Since** 字段,服务器根据此字段判断响应文件是否更新.
3. 没有更新,服务器返回一个 **304 Not Modified** 响应,通知客户端没有更新,使用本地缓存.
4. 如果有更新,就返回更新的页面响应.

```
客户端请求:

 GET / HTTP/1.1  
 Host: www.sina.com.cn:80  
 If-Modified-Since:Thu, 4 Feb 2010 20:39:13 GMT  
 Connection: Close
```

```
服务器响应:

 HTTP/1.0 304 Not Modified  
 Date: Thu, 04 Feb 2010 12:38:41 GMT  
 Content-Type: text/html  
 Expires: Thu, 04 Feb 2010 12:39:41 GMT  
 Last-Modified: Thu, 04 Feb 2010 12:29:04 GMT  
 Age: 28  
 X-Cache: HIT from sy32-21.sina.com.cn  
 Connection: close 
```

#### 持久连接

* HTTP Keep-Alive 就是 保持当前TCP连接,避免了重新建立连接.
* HTTP 长连接不可能一直保持. 例如: `Keep-Alive: timeout=5, max=100`, 表示TCP通道可以保持5s,这个长连接最多接收100次请求.
* HTTP 是无状态协议,所以,Keep-Alive也 **不能保证客户端和服务器之间的连接一定是活跃的**,唯一可以保证的是**当连接被关闭时你能得到一个通知**,所以不应该让程序**依赖于Keep-Alive的保持连接特性**,否则会有意想不到的结果.
* HTTP 长连接 如何结束传输?
1. 判断传输数据是否达到了Content-Length指示的大小.
2. 动态生成的文件没有Content-Length,是**分块传输(chunked)**,要根据 **chunked编码** 来判断,chunked编码的数据在最后有一个 **空chunked块**,表明本次传输数据结束,详情[这里][Content_Length].

#### 会话跟踪

* 会话: 客户端发出请求到服务器响应请求的过程.
* 会话跟踪: **监视**同一个用户对服务器的连续请求和接收响应.
* 为什么需要: HTTP协议是无状态的,不能保存客户信息,需要**判断是否为同一用户**,才有了会话跟踪技术.
* 常用的方法:
1. URL重写: URL结尾添加一个附加数据以标识该会话,把会话ID通过URL信息传递来区分不同的用户.
2. 隐藏表单域: 会话ID添加到**HTML表单元素**中提交,不在客户端显示.
3. Cookie: Cookie是Web服务器发送给客户端的一小段信息.客户端每次请求,服务器将Cookie发送到客户端保存以便下次使用.客户端请求时读取信息发送到服务器,进而进行用户的识别.
> 有2种方式保存Cookie:  
> a. 临时Cookie: 保存在客户端内存中,浏览器关闭后Cookie对象将消失.  
> b. 永久Cookie: 保存在客户端磁盘中,只要访问该网站,将读取Cookie发送.前提是Cookie在有效期内.
4. Session: 每个用户不同的Session, 每个用户独享, 可以存放信息. 在服务端创建一个Session对象,产生一个SessionID来标识Session对象,将SessionID放入Cookie中发送到客户端,下一次访问,SessionID发送到服务器,在服务器进行识别不同的用户.

#### 跨站攻击

* CSRF 跨站请求伪造, Cross-site request forgery. 如何防范 **CSRF** 攻击:
1. POST: 关键操作只接受**POST**请求.
2. **验证码**: 每次操作需要用户进行互动,简单有效的防御了CSRF攻击.
3. 检测**Referer**: 打开新网址,之前网址保留在新页面头文件的**Referer**中,通过检查**Referer**的值,判断这个请求是否合法.问题在于服务器不是任何时候都能接收到Referer的值,所以,Referer Check一般用于监控CSRF攻击的发生,不是用来抵御攻击.
4. Token: 主流的做法. Token **足够随机** -- 不可预测, **一次性** -- 每次成功后更新Token,增加预测难度, **保密性** -- 使用POST,防止出现在URL中.
* XSS 跨站脚本攻击, Cross Site Scripting. XSS是实现CSRF诸多途径的一条,通过XSS实现的CSRF称为XSRF. 如何防范 **XSS** 攻击:
1. 最简单直接的办法: 过滤用户的输入.
> 如果不需要用户输入 HTML 的时候, 可以直接对用户的输入进行 HTML escape.  
> 当我们需要用户输入 HTML 的时候, 需要对用户输入的内容做更加小心细致的处理. 仅仅粗暴地去掉 script 标签是没有用的, 任何一个合法 HTML 标签都可以添加 onclick 一类的事件属性来执行 JavaScript. 更好的方法可能是, 将用户的输入使用 HTML 解析库进行解析, 获取其中的数据. 然后根据用户原有的标签属性, 重新构建 HTML 元素树. 构建的过程中, 所有的标签和属性都只从白名单中拿取.

***

### 二. TCP 协议

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

### 三. IP 协议

#### 	IP地址分类

* IP地址 = 网络ID + 主机ID.
* 最初设计互联网络,为了便于寻址和层次化构造网络,每个IP地址包含2个标识码(网络ID和主机ID),同一个物理网络上所有主机使用同一个网络ID,网络上一个主机有一个主机ID.
* ID地址根据网络ID不同,分为 A, B, C, D, E 5类地址.

| 地址类别 | 地址范围 | 默认网络掩码 | 网络ID最高位 | 网络ID | 主机ID | 用途 | 私有地址 | 保留地址 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| A类地址 | 1.0.0.1~126.255.255.254 | 255.0.0.0 | 0 | 1Byte | 3Byte | 政府机关 | 10.0.0.0~10.255.255.255 | 127.x.x.x |
| B类地址 | 128.0.0.1~191.255.255.254 | 255.255.0.0 | 10 | 2Byte | 2Byte | 大中型企业 | 172.16.0.0~172.31.255.255 | 169.254.x.x |
| C类地址 | 192.0.0.1~223.255.255.254 | 255.255.255.0 | 110 | 3Byte | 1Byte | 个人 | 192.168.0.0~192.168.255.255 | |
| D类地址 | 224.0.0.1~239.255.255.254 | | 1110 | | | 组播 | | |
| E类地址 | 240.0.0.1~255.255.255.254 | | 11110 | | | 保留 | | |

* 私有地址是指在互联网中不使用,而被用在局域网络中的地址.
* 127.x.x.x是A类地址的保留地址,用作 **循环测试**.
* 169.254.x.x是B类地址的保留地址,用作 **自动获取IP,却找不到DHCP服务器** 时, 分配其中IP.
* 240.0.0.0 ~ 255.255.255.254是E类地址的保留地址,用作 **Internet试验和开发**.
* 255.255.255.255是广播地址, 0.0.0.0是当前主机地址.

#### 广播与多播

* 广播与多播仅用于 **UDP**. (TCP是面向连接的).

* 广播有4种广播地址:
1. **受限的广播**: 地址为 **255.255.255.255**,用于主机配置过程中IP数据报的目的地址,路由不转发目的地址为255.255.255.255的数据报,仅在本地网络中使用.
2. **指向网络的广播**: 地址为 **主机ID全为1** 的地址. 例如: A类网络广播地址: NetID.255.255.255,(NetID为A类网络的网络号). 一个路由必须转发指向网络的广播,但也必须有一个不进行转发的选择.
3. **指向子网的广播**: 地址为 **主机ID全为1且有特定子网号** 的地址.作为子网直接广播地址的IP地址需要了解子网的掩码. 例如: 路由收到128.1.2.255的数据报,若B类网络128.1的掩码为255.255.255.0,该地址就是指向子网的广播地址,但如果掩码为255.255.254.0,该地址就不是指向子网的广播地址.
4. **指向所有子网的广播**: 地址为 **子网号和主机号为全1** 的地址. 这种广播是陈旧过时的,更好的方式是使用多播而不是对所有子网的广播.

* 多播, 又叫 **组播**, D类地址的 **低28位** 用来多播组号.多播组地址包含高4位 **1110** 和低28位 **多播组号**.范围为 224.0.0.0 ~ 239.255.255.255.
* 多播减少了对与应用无关主机的处理负荷.
* 多播特点:
1. 允许一个或多个发送者(组播源)发送单一数据报到多个接受者.
2. 节省网络带宽.
3. 在节省网络资源的前提下保证服务质量.

#### BGP

* 边界网关协议(BGP)是运行于TCP上一种自治系统的路由协议.
* BGP是唯一一个用来处理像因特网大小的网络协议,也是唯一能够妥善处理好不相关路由域间的多路连接的协议.
* BGP是一种外部网关协议(Exterior Gateway Protocol,EGP),与OSPF,RIP等内部网关协议(Interior Gateway Protocol,IGP)不同,BGP不在于发现和计算路由,而在于控制路由的传播和选择最佳路由.
* BGP使用TCP作为其传输层协议(端口号179),提高了协议的可靠性.
* BGP既不是纯粹的矢量距离协议,也不是纯粹的链路状态协议.
* BGP支持CIDR(Classless Inter-Domain Routing,无类别域间路由).
* 路由更新时,BGP只发送更新的路由,大大减少了BGP传播路由所占用的带宽,适用于在Internet上传播大量的路由信息.
* BGP路由通过携带AS路径信息彻底解决路由环路问题.
* BGP提供了丰富的路由策略,能够对路由实现灵活的过滤和选择.
* BGP易于扩展,能够适应网络新的发展.

***

### 四. Socket 编程

* Socket是对 **TCP/IP协议簇** 的一种封装,是应用层与TCP/IP协议簇通信的 **中间软件抽象层**.

#### 简易的 WebServer 实例

* 流程如下:
1. 建立连接: 接受一个客户端连接.
2. 接受请求: 从网络中读取一条 HTTP 请求报文.
3. 处理请求: 访问资源.
4. 构建响应: 创建带有 **Header** 的 HTTP 响应报文.
5. 发送响应: 传给客户端.

* 函数逻辑如下:
1. socket(): 创建套接字.
2. bind(): 分配套接字地址.
3. listen(): 等待连接请求.
4. accept(): 允许连接请求.
5. read()/write(): 数据交换.
6. close(): 关闭连接.

```C
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <unistd.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <string>
#include <cstring>
#include <iostream>

using namespace std;

const int port = 9090;
const int buffer_size = 1<<20;
const int method_size = 1<<10;
const int filename_size = 1<<10;
const int common_buffer_size = 1<<10;

void handleError(const string &message);
void requestHandling(int *sock);
void sendError(int *sock);
void sendData(int *sock, char *filename);
void sendHTML(int *sock, char *filename);
void sendJPG(int *sock, char *filename);

int main()
{
    int server_sock;
    int client_sock;

    struct sockaddr_in server_address;
    struct sockaddr_in client_address;

    socklen_t client_address_size;

    server_sock = socket(PF_INET, SOCK_STREAM, 0);

    if (server_sock == -1)
    {
        handleError("socket error");
    }

    memset(&server_address,0,sizeof(server_address));
    server_address.sin_family = AF_INET;
    server_address.sin_addr.s_addr = htonl(INADDR_ANY);
    server_address.sin_port = htons(port);

    if(bind(server_sock,(struct sockaddr*)&server_address, sizeof(server_address)) == -1){
        handleError("bind error");
    }

    if(listen(server_sock, 5) == -1) {
        handleError("listen error");
    }

    while(true) {
        client_address_size = sizeof(client_address);
        client_sock = accept(server_sock, (struct sockaddr*) &client_address, &client_address_size);

        if (client_sock == -1) {
            handleError("accept error");
        }
        requestHandling(&client_sock);
    }

    //system("open http://127.0.0.1:9090/index.html");
    close(server_sock);

    return 0;
}

void requestHandling(int *sock){
    int client_sock = *sock;
    char buffer[buffer_size];
    char method[method_size];
    char filename[filename_size];

    read(client_sock, buffer, sizeof(buffer)-1);

    if(!strstr(buffer, "HTTP/")) {
        sendError(sock);
        close(client_sock);
        return;
    }

    strcpy(method, strtok(buffer," /"));
    strcpy(filename, strtok(NULL, " /"));

    if(0 != strcmp(method, "GET")) {
        sendError(sock);
        close(client_sock);
        return;
    }

    sendData(sock, filename);
}

void sendData(int *sock, char *filename) {
    int client_sock = *sock;
    char buffer[common_buffer_size];
    char type[common_buffer_size];

    strcpy(buffer, filename);

    strtok(buffer, ".");
    strcpy(type, strtok(NULL, "."));

    if(0 == strcmp(type, "html")){
        sendHTML(sock, filename);
    }else if(0 == strcmp(type, "jpg")){
        sendJPG(sock, filename);
    }else{
        sendError(sock);
        close(client_sock);
        return ;
    }
}

void sendHTML(int *sock, char *filename) {
    int client_sock = *sock;
    char buffer[buffer_size];
    FILE *fp;

    char status[] = "HTTP/1.0 200 OK\r\n";
    char header[] = "Server: A Simple Web Server\r\nContent-Type: text/html\r\n\r\n";

    write(client_sock, status, strlen(status));
    write(client_sock, header, strlen(header));

    fp = fopen(filename, "r");
    if(!fp){
        sendError(sock);
        close(client_sock);
        handleError("failed to open file");
        return ;
    }

    fgets(buffer,sizeof(buffer), fp);
    while(!feof(fp)) {
        write(client_sock, buffer, strlen(buffer));
        fgets(buffer, sizeof(buffer), fp);
    }

    fclose(fp);
    close(client_sock);
}

void sendJPG(int *sock, char *filename) {
    int client_sock = *sock;
    char buffer[buffer_size];
    FILE *fp;
    FILE *fw;

    char status[] = "HTTP/1.0 200 OK\r\n";
    char header[] = "Server: A Simple Web Server\r\nContent-Type: image/jpeg\r\n\r\n";

    write(client_sock, status, strlen(status));
    write(client_sock, header, strlen(header));

    fp = fopen(filename, "rb");
    if(NULL == fp){
        sendError(sock);
        close(client_sock);
        handleError("failed to open file");
        return ;
    }

    fw = fdopen(client_sock, "w");
    fread(buffer, 1, sizeof(buffer), fp);
    while (!feof(fp)){
        fwrite(buffer, 1, sizeof(buffer), fw);
        fread(buffer, 1, sizeof(buffer), fp);
    }

    fclose(fw);
    fclose(fp);
    close(client_sock);
}

void handleError(const string &message) {
    cout<<message;
    exit(1);
}

void sendError(int *sock){
    int client_sock = *sock;

    char status[] = "HTTP/1.0 400 Bad Request\r\n";
    char header[] = "Server: A Simple Web Server\r\nContent-Type: text/html\r\n\r\n";
    char body[] = "<html><head><title>Bad Request</title></head><body><p>400 Bad Request</p></body></html>";

    write(client_sock, status, sizeof(status));
    write(client_sock, header, sizeof(header));
    write(client_sock, body, sizeof(body));
}
```

***

此文参考于 [hit-alibaba.github.io][hit-alibaba.github.io],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Get_Post_Diff]: http://www.cnblogs.com/hyddd/archive/2009/03/31/1426026.html
[Content_Length]: http://www.cnblogs.com/skynet/archive/2010/12/11/1903347.html
[hit-alibaba.github.io]: https://hit-alibaba.github.io/interview/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[three_way_handshake]: /assets/images/network/tcp-connection-made-three-way-handshake.png 'three_way_handshake'
[four_way_handshake]: /assets/images/network/tcp-connection-closed-four-way-handshake.png 'four_way_handshake'