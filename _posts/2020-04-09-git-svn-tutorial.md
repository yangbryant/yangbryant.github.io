---
layout: post
title: Git SVN 操作小记
author: Srefan
date: 2020-04-09 14:38:00 +08:00
catalog: true
tags:
    - 入门
    - Git
---

***

### 一.概述

* 当下, 虽然Git已经广泛使用, 但仍会遇到一些项目是用SVN进行版本控制, 作为一个Git用户, 遇到这种情况该怎么办? 学习SVN操作? 
* 懒惰作为程序员三大美德之一, 遇到这个问题, 肯定有人偷过懒, 而这次偷懒的工具就是 `git-svn`.

### 二.基本工作流

![git-svn-basic-workflow][Image_1]

* 拉取远程仓库的代码到本地, 使用 `git svn clone svn://svn.com/user/repo`.
> `git svn clone svn://svn.com/user/repo`   
> => `svn checkout svn://svn.com/user/repo`

* 远程代码仓库有更新, 使用 `git svn rebase` 来获取更新, 
> `git svn rebase` => `svn update`.

* 工作区操作 git 正常操作即可.

* 提交到本地仓库后, 推送到远程代码仓库, 使用 `git svn dcommit`.
> `git svn dcommit` => `svn commit`

### 三.注意

* **svn repo** 建立 **branch** 和 **tag** 没有什么优势, 推荐直接使用 **svn** 命令直接操作.
* `git svn clone` 操作之后本地的 **repo** 是一个标准的 **git repo**, 可以进行所有的 **git** 命令操作, 同理, **svn** 命令就不可以操作了.
* 由于本地是一个标准的 **git repo**, 可以为 **repo** 建立 **remote upstream repo**, 这样就可以在 **local git repo**, **remote git repo** 和 **remote svn repo** 之间进行代码变更的同步.

### 四.参考链接

此文参考于 [Tony Bai的个人Blog][Link_1],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: https://tonybai.com/2019/06/25/using-git-with-svn-repo/

[Image_1]: /assets/images/git-svn/git-svn-basic-workflow.png