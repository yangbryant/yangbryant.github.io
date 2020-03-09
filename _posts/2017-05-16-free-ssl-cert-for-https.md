---
layout: post
title: 网站配置SSL完全使用HTTPS
date: 2017-05-16 14:12:00 +08:00
author: Srefan
catalog: true
tags:
    - 技巧
    - Http
---

***

* 本文使用的证书来自提供 **免费SSL证书的机构** [Let's Encrypt][Link_1], 对免费用户, 证书的有效期为 **三个月**.

### 一.概述

* 简单了解了一下, **Let’s Encrypt** 是国外一个公共的免费SSL项目, 由 `Linux 基金会` 托管, 它的来头不小, 由 Mozilla、思科、Akamai、IdenTrust 和 EFF 等组织发起, 目的就是向网站自动签发和管理免费证书, 以便加速互联网由HTTP过渡到HTTPS, 目前 Facebook 等大公司开始加入赞助行列. 目前 Let’s Encrypt 的证书已经被 Mozilla、Google、Microsoft 和 Apple 等主流的浏览器所信任, 所以使用起来完全没有问题. 证书的申请也很简单, 官方有一套自动化的脚本 ([https://github.com/certbot/certbot][Link_3]) 可以完成所有工作.

### 二.配置方法

* 从 Github 下载脚本源码

```bash
git clone https://github.com/certbot/certbot
cd certbot
chmod +x ./certbot-auto
```

* 生成证书 (生成证书时,临时关闭ngnix服务)

```bash
./certbot-auto certonly --standalone --email liyangkobebryant@gmail.com -d srefan.online -d www.srefan.online
```

* 生成的证书放在 `/etc/letsencrypt/live/` 目录下.

```bash
ls /etc/letsencrypt/live/srefan.online/
README  cert.pem  chain.pem  fullchain.pem  privkey.pem
```

* 在 `nginx` 的配置中加入ssl服务器定义

```conf
server {
        listen 443;
        server_name www.srefan.online srefan.online;
        ssl  on;
        ssl_certificate /etc/letsencrypt/live/srefan.online/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/srefan.online/privkey.pem;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        location / {
            root     /www/srefan.online/public;
            index    index.html;
        }
}
```

* 再把原先的 `http` 部分重定向到 `https`

```conf
server {
        listen 80;
        server_name www.srefan.online srefan.online;
        return 301 https://$server_name$request_uri;
}
```

*  重新启动服务器之后, 即可看到所有链接已经变成绿色的https链接了.
*  需要注意的是, Let’s Encrypt 的证书的有效期只有三个月, 所以需要设置一个定时任务, 定期更新证书.

```bash
./certbot-auto renew --standalone --pre-hook "service nginx stop" --post-hook "service nginx start" --force-renewal
```

* 此外, 推荐一个检测网站ssl链接安全性的网站: [https://www.ssllabs.com/ssltest/index.html][Link_4]

### 三.参考链接

此文参考于 [TheCodeWay的博客][Link_2],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: https://letsencrypt.org/
[Link_2]: https://thecodeway.com/blog/?p=1447/
[Link_3]: https://github.com/certbot/certbot
[Link_4]: https://www.ssllabs.com/ssltest/index.html