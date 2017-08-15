---
layout: post
title: 制作支持CocoaPods的框架
date: 2017-08-15 18:03:00 +08:00
tags: 初学者
---

***

### 一.概述

http://www.cocoachina.com/ios/20160415/15939.html
http://www.jianshu.com/p/327c667d9f1a

### 二.流程

创建podspec文件: pod spec create WPluse
编辑podspec文件
在Github上创建release版本

注册CocoaPods账号: pod trunk register 邮箱地址 '用户名' --description='描述信息' --verbose
邮箱激活
查看激活信息: pod trunk me

验证podspec文件: pod spec lint --verbose --allow-warnnings
上传CocoaPods: pod trunk push WPluse.podspec --allow-warnings
搜索是否上传成功: pod search WPluse


```
提交结果:
Updating spec repo `master`
--------------------------------------------------------------------------------
 🎉  Congrats

 🚀  WPluse (1.0.2) successfully published
 📅  August 15th, 04:01
 🌎  https://cocoapods.org/pods/WPluse
 👍  Tell your friends!
--------------------------------------------------------------------------------
```

```
搜索结果:
-> WPluse (1.0.2)
   WPluse is a simple animation similar to Alipay explor-animation.
   pod 'WPluse', '~> 1.0.2'
   - Homepage: https://srefan.online
   - Source:   https://github.com/yangbryant/WPluse.git
   - Versions: 1.0.2 [master repo]
```

### 三.学习


### 四.参考链接

此文参考于 [TheCodeWay的博客][Link_1],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: https://letsencrypt.org/