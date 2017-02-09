---
layout: post
title: 四种常见的 POST 提交数据方式
date: 2017-02-09 10:20:00 +08:00
tags: Http协议
---

***

HTTP/1.1 协议规定的 HTTP 请求方法有 OPTIONS, GET, HEAD, POST, PUT, DELETE, TRACE, CONNECT 几种. 其中 POST 一般用来向服务端提交数据,本文主要讨论 POST 提交数据的四种方式.

我们知道, HTTP 协议以 ASCII 码传输,建立在 TCP/IP 协议之上的应用层规范.
规范把 HTTP 请求分为三个部分: 状态行, 请求头, 消息主体.

```
<method> <request-URL> <version>
<headers>

<entity-body>
```

协议规定 POST 请求的数据必须放在消息主体(entity-body)中,但协议没有规定数据使用什么编码方式.  
但数据发送出去,服务端解析成功才有意义.服务端通常是根据请求头(headers)中的 **Content-Type** 字段来获知消息主体的编码方式,再对主体进行解析.  
POST 提交的数据方案,包含了 `Content-Type` 和`消息主体编码方式`两部分.

***

### application/x-www-form-urlencoded

* 最常见的 POST 提交数据的方式.
* 浏览器原生的 <form> 表单,如果不设置 `enctype` 属性,最终会以 `application/x-www-form-urlencoded` 方式提交数据.

```plain
POST http://www.example.com HTTP/1.1
Content-Type: application/x-www-form-urlencoded;charset=utf-8

title=title&sub%5B%5D=1&sub%5B%5D=2&sub%5B%5D=3
```

1. **Content-Type** 被指定为 `application/x-www-form-urlencoded`.
2. 提交的数据按照 `key1=val1&key2=val2` 的方式进行编码, key和val进行 URL 转码.大部分服务端语言都对这种方式有很好的支持.例如 PHP 中, `$_POST['title']` 可以获取到 title 的值, `$_POST['sub']` 可以得到 sub 数组.
3. 用 Ajax 提交数据时,也是使用这种方式. 例如 **JQuery 和 QWrap 的 Ajax**, Content-Type 默认值都是 `application/x-www-form-urlencoded;charset=utf-8`.

***

### multipart/form-data

* 又是一个常见的 POST 数据提交的方式.
* 这种方式一般用来上传文件.
* 使用表单上传文件时,必须让 **<form>** 表单的 `enctype` 等于 `multipart/form-data` .

```plain
POST http://www.example.com HTTP/1.1
Content-Type:multipart/form-data; boundary=----WebKitFormBoundaryrGKCBY7qhFd3TrwA

------WebKitFormBoundaryrGKCBY7qhFd3TrwA
Content-Disposition: form-data; name="text"

title
------WebKitFormBoundaryrGKCBY7qhFd3TrwA
Content-Disposition: form-data; name="file"; filename="chrome.png"
Content-Type: image/png

PNG ... content of chrome.png ...
------WebKitFormBoundaryrGKCBY7qhFd3TrwA--
```

1. 首先,生成了一个 **boundary** 用于分割不同的字段,为了避免与正文内容重复, boundary 很长很复杂.
2. 然后, Content-Type 指明了数据是以 `multipart/form-data` 来编码, 和 本地请求的 boundary 的内容.
3. 消息主体里按照 **字段个数** 分为多个结构类似的部分,每部分都是以 `--boundary` 开始,紧接着是内容描述信息,回车,最后是字段具体内容(文本或二进制).如果传输的是文件,还要包含文件名和文件类型信息.最后以 `--boundary--` 标示结束.

* 关于 `multipart/form-data` 的详细定义,请前往 [rfc1867][rfc1867] 查看.

* 上面提到的这两种 POST 数据的方式,都是浏览器原生支持的,而且现阶段标准中原生 **<form>** 表单也只支持这两种方式(通过 **<form>** 元素的 enctype 属性指定,默认为 application/x-www-form-urlencoded.其实 enctype 还支持 text/plain,不过用得非常少)

***

### application/json

* `application/json` 作为 `Content-Type` 响应头,用来通知服务端消息主体是序列化后的 **JSON 字符串**.
* JSON 格式支持比键值对复杂得多的结构化数据.

```plain
POST http://www.example.com HTTP/1.1 
Content-Type: application/json;charset=utf-8

{"title":"test","sub":[1,2,3]}
```

* 这种方案可以方便的提交复杂的结构化数据,特别适合 **RESTful** 的接口.
* 各大抓包工具如 Chrome 自带的开发者工具,Firebug,Fiddler,都会以树形结构展示 JSON 数据,非常友好.
* 但也有些服务端语言还没有支持这种方式,例如 php 就无法通过 `$_POST` 对象从上面的请求中获得内容.需要自己动手处理下:在请求头中 `Content-Type` 为 `application/json` 时,从 `php://input` 里获得原始输入流,再 `json_decode` 成对象.

***

### text/xml

* XML-RPC(XML Remote Procedure Call).它是一种使用 HTTP 作为传输协议,XML 作为编码方式的远程调用规范.

```plain
POST http://www.example.com HTTP/1.1 
Content-Type: text/xml

<?xml version="1.0"?>
<methodCall>
    <methodName>examples.getStateName</methodName>
    <params>
        <param>
            <value><i4>41</i4></value>
        </param>
    </params>
</methodCall>
```

* XML-RPC 协议简单,功能够用,各种语言的实现都有.
* 它的使用也很广泛,如 WordPress 的 XML-RPC Api,搜索引擎的 ping 服务等等.
* JavaScript 中,也有现成的库支持以这种方式进行数据交互,能很好的支持已有的 XML-RPC 服务.不过,个人觉得 XML 结构还是过于臃肿,一般场景用 JSON 会更灵活方便.

***

此文参考于 [Jerry Qu 博客][imququ.com],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[imququ.com]: https://imququ.com/post/four-ways-to-post-data-in-http.html
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[rfc1867]: (http://www.ietf.org/rfc/rfc1867.txt
