---
layout: post
title: HTTP 状态码
date: 2017-02-13 11:38:00 +08:00
author: Srefan
catalog: true
tags:
    - 入门
    - 协议
    - Http
---

***

#### 概述

* HTTP 状态码作用是 **Web服务器通知客户端此次请求的状态**, 用3位数字代码表示Web服务器HTTP响应状态.
* 状态码位于 HTTP Response 的第一行中,返回 **状态码** 和 **状态消息**.

* 如下,Web服务器会返回 **HTTP/1.1 404 Not Found** 来告诉客户端,服务器无法找到所请求的URL.

```plain
HTTP/1.1 404 Not Found
X-Powered-By: Express
Date: Mon, 13 Feb 2017 03:27:03 GMT
Connection: close
Content-Length: 0
```

***

#### 分类

* HTTP状态码分为 **五大类**.

| | 范围 | 分类 |
| --- | --- | --- |
| 1xx | 100 ~ 102 | 信息提示 |
| 2xx | 200 ~ 207 | 成功 |
| 3xx | 300 ~ 307 | 重定向 |
| 4xx | 400 ~ 451 | 客户端错误 |
| 5xx | 500 ~ 510 | 服务器错误 |

***

#### 状态码

| 类别 | 状态码 | 状态消息 | 含义 |
| --- | --- | --- | --- |
| 1xx | 100 | Continue | 收到了请求的起始部分,客户端应该继续请求. |
| | 101 | Switching Protocols | 服务器正根据客户端的指示将协议切换成Update Header列出的协议. |
| | 102 | Processing | 处理将被继续执行. |
| 2xx | 200 | OK | 服务器成功处理了请求. |
| | 201 | Created | 请求已经被实现,而且有一个新的资源已经依据请求的需要而创建,且其URI已经随Location头信息返回. |
| | 202 | Accepted | 请求已接受,但服务器尚未处理. |
| | 203 | Non-Authoritative Information | 服务器已将事务成功处理,只是实体Header包含的信息不是来自原始服务器,而是来自资源的副本. |
| | 204 | No Content | Response中包含一些Header和一个状态行,但不包括response body. |
| | 205 | Reset Content | 服务器成功处理了请求,且没有返回任何内容,与204不同,浏览器应该重置当前页面上所有的HTML表单. |
| | 206 | Partial Content | 服务器已经成功处理了部分GET请求. |
| | 207 | Multi-Status | 之后的消息体将是一个XML消息,并且可能依照之前子请求数量的不同,包含一系列独立的响应代码. |
| 3xx | 300 | Multiple Choices | 客户端请求了实际指向多个资源的URL,和一个选项列表一起返回,用户可以选择他希望的选项. |
| | 301 | Moved Permanently | 被请求的资源已永久移动到新位置. |
| | 302 | Found | 请求的资源现在临时从不同的URI响应请求. |
| | 303 | See Other | 对应当前请求的响应可以在另一个URI上被找到,而且客户端应当采用GET的方式访问那个资源. |
| | 304 | Not Modified | 客户的缓存资源是最新的,要客户端使用缓存. |
| | 305 | Use Proxy | 被请求的资源必须通过指定的代理才能被访问. |
| | 306 | Switch Proxy | ~~在最新版的规范中，306状态码已经不再被使用.~~ |
| | 307 | Temporary Redirect | 请求的资源现在临时从不同的URI响应请求. |
| 4xx | 400 | Bad Request | 由于包含语法错误,当前请求无法被服务器理解. |
| | 401 | Unauthorized | 当前请求需要用户验证. |
| | 402 | Payment Required | 为了将来可能的需求而预留的. |
| | 403 | Forbidden | 服务器已经理解请求,但是拒绝执行它. |
| | 404 | Not Found | 请求失败,请求所希望得到的资源未被在服务器上发现. |
| | 405 | Method Not Allowed | 请求行中指定的请求方法不能被用于请求相应的资源. |
| | 406 | Not Acceptable | 请求的资源的内容特性无法满足请求头中的条件,因而无法生成响应实体. |
| | 407 | Proxy Authentication Required | 客户端必须在代理服务器上进行身份验证. |
| | 408 | Request Timeout | 请求超时. |
| | 409 | Conflict | 由于和被请求的资源的当前状态之间存在冲突,请求无法完成. |
| | 410 | Gone | 被请求的资源在服务器上已经不再可用,而且没有任何已知的转发地址. |
| | 411 | Length Required | 服务器拒绝在没有定义Content-Length头的情况下接受请求. |
| | 412 | Precondition Failed | 服务器在验证在请求的头字段中给出先决条件时,没能满足其中的一个或多个. |
| | 413 | Request Entity Too Large | 请求提交的实体数据大小超过了服务器愿意或能够处理的范围. |
| | 414 | Request-URI Too Long | 请求的URI长度超过了服务器能够解释的长度. |
| | 415 | Unsupported Media Type | 对于当前请求的方法和所请求的资源,请求中提交的实体并不是服务器中所支持的格式. |
| | 416 | Requested Range Not Satisfiable | 请求中Range中任何数据范围都与当前资源的可用范围不重合,同时请求没有定义If-Range请求头. |
| | 417 | Expectation Failed | 在请求头Expect中指定的预期内容无法被满足. |
| | 418 | I'm a teapot | 超文本咖啡壶控制协议中定义的,当一个控制茶壶的HTCPCP收到BREW或POST指令要求其煮咖啡时应当回传此错误. |
| | 421 | There are too many connections from your internet address | 客户端的IP地址到服务器的连接数超过了服务器许可的最大范围. |
| | 422 | Unprocessable Entity | 请求格式正确,但是由于含有语义错误,无法响应. |
| | 423 | Locked | 当前资源被锁定. |
| | 424 | Failed Dependency | 由于之前的某个请求发生的错误,导致当前请求失败. |
| | 425 | Unordered Collection | 在WebDav Advanced Collections草案中定义,但未出现在\<WebDAV顺序集协议\>. |
| | 426 | Upgrade Required | 客户端应当切换到TLS/1.0. |
| | 449 | Retry With | 由微软扩展,请求应当在执行完适当的操作后进行重试. |
| | 451 | Unavailable For Legal Reasons | 因法律的要求而被拒绝. |
| 5xx | 500 | Internal Server Error | 服务器遇到了一个未曾预料的状况,导致了它无法完成对请求的处理. |
| | 501 | Not Implemented | 服务器不支持当前请求所需要的某个功能. |
| | 502 | Bad Gateway | 作为网关或代理工作的服务器尝试执行请求,从上游服务器接收到无效的响应. |
| | 503 | Service Unavailable | 由于临时的服务器维护或者过载,服务器当前无法处理请求. |
| | 504 | Gateway Timeout | 作为网关或代理工作的服务器尝试执行请求,未及时从上游服务器或辅助服务器(例DNS)收到响应. |
| | 505 | HTTP Version Not Supported | 服务器不支持,或者拒绝支持在请求中使用的HTTP版本. |
| | 506 | Variant Also Negotiates | 服务器存在内部配置错误. |
| | 507 | Insufficient Storage | 服务器无法存储完成请求所必须的内容. |
| | 509 | Bandwidth Limit Exceeded | 服务器达到带宽限制. |
| | 510 | Not Extended | 获取资源所需要的策略并没有被满足. |

本文参考于[维基百科][wikipedia],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[wikipedia]: https://en.wikipedia.org/wiki/List_of_HTTP_status_codes 'wikipedia'
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/
