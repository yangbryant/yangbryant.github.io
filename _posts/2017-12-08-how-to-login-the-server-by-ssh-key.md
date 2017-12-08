---
layout: post
title: 怎样用 SSH-Key 登录服务器
date: 2017-12-08 15:50:00 +08:00
tags: 配置师
---

***

### 一.概述

* 此教程是对 MacOS 和 Linux 用户适用的. 
* 通常我们登录远程服务器使用 IP, 用户名, 密码的方式, 从安全角度考虑, 防止脚本暴力破解, 推荐使用 **SSH-Key 登录**.
* 此外, 目前还有一种 **denyhosts** 的方式防止暴力破解.
* SSH-Key 是客户端与服务器之间建立的一对密钥, 如果服务器和客户端的密钥匹配, 则允许服务器进行连接.

### 二.流程

#### Step1. 创建 RSA 密钥对

* 第一步,在客户端创建密钥对.

```bash
ssh-kekgen -t rsa
```

#### Step2. 存储密钥

* 输入 `keygen` 命令后, 会有以下几个问题, 简单处理就是, 一路回车键.

```bash
# 设置存储路径
Enter file in which to save the key (/demo/.ssh/id_rsa):
```

```bash
# 设置密钥密码
Enter passphrase (empty for no passphrase):
```

* 完成后, 公钥保存在 `/demo/.ssh/id_rsa.pub`, 私钥保存在 `/demo/.ssh/id_rsa`.

```bash
ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/demo/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /demo/.ssh/id_rsa.
Your public key has been saved in /demo/.ssh/id_rsa.pub.
The key fingerprint is:
4a:dd:0a:c6:35:4e:3f:ed:27:38:8c:74:44:4d:93:67 demo@a
The key's randomart image is:
+--[ RSA 2048]----+
|          .oo.   |
|         .  o.E  |
|        + .  o   |
|     . = = .     |
|      = S = .    |
|     o + = +     |
|      . o + o .  |
|           . o   |
|                 |
+-----------------+
```

#### Step3. 密钥添加到服务器

* 添加公钥到服务器的 `authorized_keys` 文件.

```bash
cat ~/.ssh/id_rsa.pub | ssh root@[your.ip.address.here] "cat >> ~/.ssh/authorized_keys"
```

#### Step4. 登录服务器

* 使用私钥登录服务器.

```bash
ssh -i [your.private.key.here] root@[your.ip.address.here]
```

#### Step5. 禁用密码登录, 只允许 SSH-Key 登录

* 如果你确认你可以用 **SSH-Key** 方式成功登录, 你可以禁用密码登录.
* 需要编辑服务器的 **SSHd** 配置文件 `/etc/ssh/sshd_config`:

```vi
# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords yes
PermitEmptyPasswords no
#PasswordAuthentication yes
PasswordAuthentication no
```

#### Step6. 重启服务

* 重启 `sshd` 服务, 大功告成.

```bash
service sshd restart
```

### 三.参考链接

此文参考于 [digitalocean的文档][Link_1],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets