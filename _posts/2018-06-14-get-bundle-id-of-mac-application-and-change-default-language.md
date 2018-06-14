---
layout: post
title: macOS获取应用BundleID并修改默认语言
date: 2018-06-14 14:00:00 +08:00
tags: 配置师
---

***

### 一.获取Bundle ID

> 第一种办法: `osascript -e 'id of app "SomeApp"'`

* 举例如下:

```shell
# MacBookPro:~ srefan$ osascript -e 'id of app "Safari"'
com.apple.Safari
```

> 第二种办法: `mdls -name kMDItemCFBundleIdentifier -r SomeApp.app`

* 举例如下:

```shell
MacBookPro:~ srefan$ mdls -name kMDItemCFBundleIdentifier -r /Applications/Safari.app/
com.apple.Safari
```

### 二.修改默认语言

> `defaults write bundle_id AppleLanguages '(lang1, lang2)'`

* 举例如下:

```shell
# 简体中文优先, 英文次之
defaults write com.apple.Safari AppleLanguages '(zh-CN, en-US)'
```

### 四.参考链接

此文参考于 [stackoverflow的问答][Link_2]和[知乎点儿鱼的回答][Link_1],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: https://www.zhihu.com/question/22327155/answer/184561570
[Link_2]: https://stackoverflow.com/questions/39464668/how-to-get-bundle-id-of-mac-application/39464824