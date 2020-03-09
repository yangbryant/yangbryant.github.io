---
layout: post
title: Vim强制保存只读文件的方法
date: 2020-03-08 16:24:00 +08:00
author: Srefan
catalog: true
tags:
    - Linux
    - Vi
    - 技巧
---

***

### 一.概述

* 在使用vim时, 当我们以普通用户去打开一个只有root用户才有权限操作的文件时, 我们编辑完成之后, 正要保存, 却发现, 这个文件我们没有权限修改.
* 每次遇到这样的问题, 我都很头疼, 好不容易把文件编辑完了, 却无法保存, 就只能放弃, 然后退出, 再以root权限打开, 重新编辑.
* 我总是相信, 所有的问题都有解决的方法.通过Google查找, 终于解决了这个问题.

### 二.解决方案

```shell
:w !sudo tee %
```

> w: 表示保存文件
> !: 表示执行外部命令
> tee: linux命令, 这个有点复杂, 可以查看linux命令帮助
> %: 在执行外部命令时, %会扩展成当前文件名; 这个%区别于替换时的%, 替换时%的意义是代表整个文件, 而不是文件名

* 上述方式非常完美的解决了不能保存只读文件的问题, 但毕竟命令还是有些长, 为了避免每次输入一长串的命令, 可以将它映射为一个简单的命令加到 `.vimrc` 中:

```shell
#Allow saving of files as sudo when I forgot to start vim using sudo.
cmap w!! w !sudo tee > /dev/null %
```

* 这样, 简单的运行`:w!!`即可. 命令后半部分 `> /dev/null` 作用为显式的丢掉标准输出的内容.

### 三.参考链接

此文参考于 [Huoty's Blog][Link_1],十分感谢.  
所有引用内容版权归原作者所有.
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/
[Link_1]: http://kuanghy.github.io/2015/12/30/sudo-vim