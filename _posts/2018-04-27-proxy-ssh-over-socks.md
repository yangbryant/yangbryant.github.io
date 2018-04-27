---
layout: post
title: ssh通过Socks代理(最简单的方式)
date: 2018-04-27 10:32:00 +08:00
tags: 我是一名译者
author: Mike Heijmans
categories: SysAdmin
origin_tags: [Tech, System Administration, Git, SSH, Tricks, DevOps]
preview: Today I had a strange problem. I needed to work with an internal git repo behind a firewall from my home on my personal laptop. The git server only allows SSH connections (git@git.corp.example.com) and doesn't support HTTP requests.
og_type: article
og_description: The simple way to proxy SSH over a SOCKS proxy without any additional software.
disqus_id: 3
---

***

今天,我遇到一个奇怪的问题.我需要在家用我的私人电脑穿透防火墙访问公司内部的git仓库(没有VPN客户端).但是公司的git服务器(git@git.corp.example.com)只允许SSH链接,不支持HTTP请求.

> 注意: git对 [HTTP协议的仓库](http://www.aireadfun.com/blog/2013/08/27/using-git-through-a-socks-proxy-or-ssh-tunnel/) 支持HTTP代理.

```shell
# HTTP代理方法, 译者注
git config --global http.proxy 'socks5://127.0.0.1:1080'
git config --global https.proxy 'socks5://127.0.0.1:1080'
```

总之,在网上有一堆解决基于SSH仓库代理的办法,但是大部分让人疑惑,或者需要额外的软件.

![img-responsive](/assets/images/proxy-ssh-over-socks/splat.gif)

**那...有更好的办法?**

有一个更好的办法,我向你展示如何只使用OpenSSH和netcat中内置的功能以最小影响做到这一点.

### 1. 安装Socks代理

如果你还不知道Socks代理,你可以用OpenSSH创建一个.

*(这对于网页浏览十分有用,就像你通过SSH连接的盒子上一样)*

你可以通过添加 `-D` 参数来监听端口:

```
ssh -D 1080 jumphost.corp.example.com
```

这样操作, 你就像正常的SSH会话一样连接到 `jumphost.corp.example.com`, 它还会在`localhost:1080`上设置一个SOCKS代理.你可以打开浏览器的网络设置并设置socks代理到localhost的1080端口上来测试此功能.

现在,当你浏览网页时,来自`jumphost.corp.example.com`的请求会通过`OpenSSH` `-D`参数端口的SOCKS服务器传输.*十分漂亮哈?*


### 2. 在SSH配置中添加代理

所以,现在你有一个在`localhost:1080`上运行的SOCKS代理.你可以告诉`OpenSSH`使用该隧道来处理针对特定hostname的所有请求.
我们会用`netcat`(nc)来执行此操作.

添加下面的参数到你的 ```~/.ssh/config```文件中:

```
Host git.corp.example.com
  ProxyCommand=nc -X 5 -x localhost:1080 %h %p
```

```
# 参数介绍, 译者注

-X 5 表示 socks5
-X 4 表示 socks4
-X connect 表示 https

-x 当前代理的端口和地址

%h 目标hostname,请求传递的参数
%p 目标端口,请求传递的参数

```

正如你看到的,当我们执行```ssh git.corp.example.com```时, 事实上OpenSSH会用`netcat`通过`localhost:1080`代理网络数据流.

> 注意: 如果你不知道 `netcat`, 你应该了解一下! 它能通过网络连接发送原始数据流.

事实上,此操作十分优雅.之前`OpenSSH`直接发送到`git.corp.example.com`的数据流现在是通过我们用netcat创建(步骤1)的SOCKS隧道发送的.

### 3. 像大佬一样用Git

现在,我们在本地启动了一个SOCKS代理(步骤1),并告诉`OpenSSH`任何来自`git.corp.example.com`的请求都用代理执行.  
没有什么其他要做的了,可以像往常一样使用Git了.

```
git clone git@git.corp.example.com/parabuzzle/jedimaster.git
```

这样就能拉取到我需要的内部代码.(不需要VPN客户端).

**完成了! 喝杯啤酒, 享受下SysAdmin魔法的光辉!**


### 4.参考链接

此文原文链接 [mikeheijmans.com][Link_1].  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: http://www.mikeheijmans.com/sysadmin/2014/08/12/proxy-ssh-over-socks/