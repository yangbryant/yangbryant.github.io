---
layout: post
title: Jekyll 服务在局域网内访问预览
date: 2016-12-20 15:35:24 +08:00
tags: Jekyll 配置
---

**Jekyll** 通过 `bundle exec jekyll serve` 指令启动服务, 生成静态html, 通过http服务给 **localhost** 进行调试预览.  
  
服务启动信息如下:

```
MacBookPro:~ user$ bundle exec jekyll serve

Configuration file: /Users/user/yangbryant.github.io/_config.yml
            Source: /Users/user/yangbryant.github.io
       Destination: /Users/user/yangbryant.github.io/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
                    done in 1.106 seconds.
 Auto-regeneration: enabled for '/Users/user/yangbryant.github.io'
Configuration file: /Users/user/yangbryant.github.io/_config.yml
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```
  
如输出信息, Jekyll 服务绑定 `http://127.0.0.1:4000/` 地址上, 局域网内是不能访问 Jekyll 服务的.  
其实, 只需要增加 Jekyll 的参数, 带上 `--host=0.0.0.0` .  
即使用此启动服务的指令=> `bundle exec jekyll serve --host=0.0.0.0` .

```
MacBookPro:~ user$ bundle exec jekyll serve --host=0.0.0.0

Configuration file: /Users/user/yangbryant.github.io/_config.yml
            Source: /Users/user/yangbryant.github.io
       Destination: /Users/user/yangbryant.github.io/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
                    done in 0.914 seconds.
 Auto-regeneration: enabled for '/Users/user/yangbryant.github.io'
Configuration file: /Users/user/yangbryant.github.io/_config.yml
    Server address: http://0.0.0.0:4000/
  Server running... press ctrl-c to stop.
```

服务启动后,通过 `IP地址` + `端口号` (默认为4000)即可在局域网内访问.