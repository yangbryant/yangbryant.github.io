---
layout: post
title: Linux下补丁的生成与导入
date: 2020-03-07 13:38:00 +08:00
tags: 记录仪
---

***

### 一.概述

> 做Linux应用基础模块的时候,遇到了向下一环节开发人员释放了源码之后发现了bug的情况.
> 修复bug之后,怎样去更新源码更优雅呢?重新释放一版源码肯定不是最佳的方式,有一种优雅的操作就是提供基于源码的补丁包.

### 二.生成补丁

* 打补丁有2种方式,一种是 `diff`, 一种是 `git diff`.

#### 1. diff方式

```shell
diff -Nuar sources-root-directory fixed-root-directory > changes-fix-error.patch
```

#### 2. git方式

```shell
git show HEAD > changes-fix-error.patch
```

### 三.导入补丁

```shell
# -p忽略的目录级数, -p1指忽略一级目录; -b生成一个.org之前源文件的备份
patch -bp1 < changes-fix-error.patch
```

### 四.撤销补丁

```shell
patch -Rp1 < changes-fix-error.patch
```

### 五.其他

> 其实还有一种 `git format-patch` 方式, 导入用 `git am` 处理.

* git log 信息如下:

![git-log](/assets/images/patch/patch-git-log.png)

* 执行 `git format-patch 8c177bf` 命令后导出patch包.

![git-log](/assets/images/patch/patch-export-changes.png)

* 检查patch文件

```shell
iray@IRay:~$ git apply --stat 0001-client-add-send-cmd-recv-cmd-callback.patch
 .../examples/example_irnet.c                       |   46 ++++++++++++++++++--
 network_transmission_client/src/IrNetClient.c      |   10 ++++
 network_transmission_client/src/IrNetClient.h      |    6 +++
 network_transmission_client/src/IrNetCmd.c         |   12 +++++
 network_transmission_client/src/IrNetCmd.h         |   16 +++++++
 network_transmission_client/src/IrNetData.c        |   31 ++++++++++---
 network_transmission_client/src/IrNetData.h        |   12 ++++-
 network_transmission_client/src/IrTcpClient.c      |   19 +++++++-
 network_transmission_client/src/IrTcpClient.h      |    4 ++
 network_transmission_client/src/IrTcpCommon.h      |    5 ++
 10 files changed, 145 insertions(+), 16 deletions(-)
```

* 检查能否应用成功

```shell
git apply --check 0001-client-add-send-cmd-recv-cmd-callback.patch
```

* 导入补丁

```shell
git am --signoff < git apply --check 0001-client-add-send-cmd-recv-cmd-callback.patch
```

### 六.参考链接

使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/


[Image]:patch-git-log.png