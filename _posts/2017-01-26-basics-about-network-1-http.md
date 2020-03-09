---
layout: post
title: 计算机网络基础(一) -- HTTP协议
date: 2017-01-26 10:58:59 +08:00
author: Srefan
catalog: true
tags:
    - 入门
    - Http
---

***

#### 计算机网络基础:

[计算机网络基础(一) -- HTTP协议][http_basic]  
[计算机网络基础(二) -- TCP协议][tcp_basic]  
[计算机网络基础(三) -- IP协议][ip_basic]  
[计算机网络基础(四) -- Socket编程实例][socket_programming]  

***

### HTTP 协议

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

此文参考于 [hit-alibaba.github.io][hit-alibaba.github.io],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Get_Post_Diff]: http://www.cnblogs.com/hyddd/archive/2009/03/31/1426026.html
[Content_Length]: http://www.cnblogs.com/skynet/archive/2010/12/11/1903347.html
[hit-alibaba.github.io]: https://hit-alibaba.github.io/interview/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[http_basic]: /2017/01/basics-about-network-1-http/
[tcp_basic]: /2017/01/basics-about-network-2-tcp/
[ip_basic]: /2017/01/basics-about-network-3-ip/
[socket_programming]: /2017/01/basics-about-network-4-socket-programming/