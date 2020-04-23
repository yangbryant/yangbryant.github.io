---
layout: post
title: NFS 服务配置与使用
subtitle: 
author: Srefan
date: 2020-04-22 17:28:00 +08:00
catalog: true
header-img: 
tags:
    - 入门
    - 软件
---

***

### 一.概述

#### 1.介绍

* NFS会经常用到, 用于在网络上共享存储. 这样讲, 你对NFS可能不太了解, 笔者不妨举一个例子来说明一下NFS是用来做什么的.
* 假如有三台机器A、B、C，它们需要访问同一个目录, 目录中都是图片, 传统的做法是把这些图片分别放到A、B、C. 但是使用NFS只需要放到A上, 然后A共享给B和C即可. 访问的时候, B和C是通过网络的方式去访问A上的那个目录的.

#### 2.与FTP,Samba区别

| | NFS | FTP | Samba |
|:----:|:----:|:----:|:----:|
|名称| 网络文件系统 | 文件传输协议 | 服务信息块协议 |
| 协议 | TCP/IP | TCP/IP | Windows-CIFS 和 Service Message Block |
| 模式 | 文件共享 | 双向发送文件, 获取服务器目录列表 | Linux和Windows系统文件共享 |

### 二.流程

#### 1.安装

* ubuntu下安装如下

```
sudo apt install nfs-kernel-server
```

#### 2.配置

* NFS配置起来还是蛮简单的, 只需要编辑配置文件`/etc/exports`即可.

```shell
iray@IRay:~$ cat /etc/exports 
# /etc/exports: the access control list for filesystems which may be exported
#		to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/home *(rw,sync,no_subtree_check,no_root_squash)
```

> /home 本地要分享的目录  
> \* 允许访问的主机,可以是一个IP,可以是一个IP段, * 是通配符  
> (rw,sync,no_subtree_check,no_root_squash) 权限选项  

* rw 读写; ro 只读
* sync 同步模式, 时时写入磁盘; async 定期写入磁盘
* no_root_squash root用户对共享目录拥有至高的权限控制,和本机操作一样; root_squash root用户对共享目录的权限不高,只有普通用户的权限; all_squash 身份被限定成为一个指定的普通用户身份
* anonuid/anongid 要和 root_squash 以及 all_squash 一同使用, 用于指定使用NFS的用户限定后的uid和gid, 前提是本机的`/etc/passwd`中存在这个uid和gid

##### 举例解析:

```
# 共享的目录为/home
# 信任的主机为10.0.2.0/24这个网段
# 权限为读写, 同步, 限定所有使用者, 并且限定的uid和gid都为501
/home 10.0.2.0/24(rw,sync,all_squash,anonuid=501,anongid=501)
```

#### 启动服务

* NFS依赖`portmap`, 要先启动 portmap, 启动完再启动 NFS.

```
service portmap start
service nfs start
```

### 三.使用

#### 查看共享情况

```
# 客户端查看服务器的共享情况
showmount -e nfs_server_ip
```

#### 查看客户端列表

```
# 服务器查看连接本机的客户端列表
showmount -a
```

#### 挂载NFS服务

```
# 连接NFS服务器, nolock不加锁
mount -t nfs -o nolock 10.10.10.10:/home /mnt/nfs-server
```

#### 卸载NFS服务

```
# 卸载挂载点
umount /mnt/nfs-server
```

### 四.参考链接

此文参考于 [极客学院][Link_1],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: https://wiki.jikexueyuan.com/project/linux/nfs.html