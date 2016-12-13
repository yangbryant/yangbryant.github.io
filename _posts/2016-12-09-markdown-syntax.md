---
layout: post
title: Markdown 语法入门
date: 2016-12-09 16:12:00 +08:00
tags: 初学者
---

***

## 一.概述

**Markdown** 是一种轻量级标记语言, 由 John Gruber 设计, 目标是实现 **[易读易写]** . 本文参考 [markdown.cn][Link_1] 和 [MacDown Software][Link_2] 的帮助文档.

***

## 二.规则
### 1.标题 
标题格式在文字前加 `#` 号 ( Markdown 支持两种标题语法, `类 Setext` 和 `类 atx` 形式. 这是 `类 atx` 方法, 较为常用 ) :

```swift
# 一级标题
## 二级标题
### 三级标题
```

以此类推, 总共六级标题, 建议在 `#` 号后加一个 `空格` , 这是最标准的 Markdown 语法.

![标题][Img_1]

### 2.列表
#### 有序列表
有序列表用 `数字 + 英文句点` 的方式:

```
1. Rad
2. Green
3. Blue
```

#### 无序列表
无序列表用 `*` 或 `+` 或 `-` 的方式 ( 三种效果是一样的 ) :

```
* Red
* Green
* Blue
```

![列表][Img_2]

### 3.引用
引用别处的句子用引用的格式, 每行文本前加 `>` :

```
> 这是一段引用
```

引用可以嵌套, 也可以使用其他 Markdown 语法.

![引用][Img_3]

### 4.链接和图片
链接和图片语法接近, 区别在于开头位置的 `!` :

```
[Google](https://www.google.com/)
![Icon](http://yangbryant.github.io/assets/images/favicon.png)
```

上述为 `行内式` , 还有一种 `参考式` , 即用标记来辨识链接, 链接标记可以在文件任意处定义 :

```
[Google][Google_Link]

[Google_Link] https://www.google.com/ (Google Link)
```

有一种 `隐式链接标记` 方式, 链接标记和链接文字一致 :

```
[Google][]

[Google] https://www.google.com/ (Google Link)
```

markdown中图片不支持设置对齐方式和自定义宽和高.

链接还有一种简单的写法 <https://www.google.com> , <liyangkobebryant@gmail.com> :

```
<https://www.google.com/>
<liyangkobebryant@gmail.com>
```

![链接和图片][Img_4]

### 5.粗体和斜体
斜体用 `一个 *` 或 `一个 _` 包含, 粗体用 `两个 *` 或 `两个 _` 包含 的方式 ( `*` 和 `_` 效果是一样的 ) :

```
**这是粗体写法**
*这是斜体写法*
```

几种特殊的文字标记方式 ( 内联方式 ) :  
~~delete line~~  
<del>Much wow</del>  
<u>So doge</u>  
<mark>So good</mark>  

```
~~delete line~~
<del>Much wow</del>
<u>So doge</u>
<mark>So good</mark>
```

![粗体和斜体][Img_5]

### 6.表格
表格的格式比较复杂, 格式如下 :

```
| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |
```

需要留意的地方是用 `:` 设置对齐方式, 单一设置 `|:` 或 `:|` 为居左或居右对齐, 同时设置为居中对齐. 不设置为标题居中, 内容居左.

![表格][Img_6]

### 7.代码块
单行代码用 `` `code` ``

```
`printf("Hello worldddd!\n");`
```

```
 ```python
 def inc(x): 
     return x + 1

 print inc(10)
 ```
```

代码块中插入 `` ` `` , 可以用多个 `` ` `` 开始和结束代码, 也可以在 `` ` `` 前加 `一个空格` .

![代码块][Img_7]

### 8.分隔符
在一行中用三个以上的 `*` 或 `-` 或 `_` 来建立一个分隔线, 行内不能有其他东西. 可以在星号或是减号中间插入空格 :

```
***
----------
```
![分隔符][Img_8]

### 9.换行
特别容易忽略换行符的格式, 换行符由 `2个以上的空格 + 1个回车` 组成.

### 10.脚注
脚注的语法 :

```
这是一个脚注：[^sample_footnote]

[^ sample_footnote]: 这里是脚注信息
```

### 11.注释
注释的语法 :

```
<!-- comment -->
<!-- more -->
```

***

## 三.小结
到这里, Markdown 的基本语法在日常的使用中基本就没什么大问题了, 只要常用, 写文档很快就可以行云流水了. 更多的语法规则, 其实 [MacDown][Link_2] 的 Help 文档例子很好, 你可以随时查阅和学习语法.

[Link_1]: http://www.markdown.cn
[Link_2]: http://macdown.uranusjr.com/download/latest/

[Img_1]: /assets/images/markdown/markdown_headers.png 'headers'
[Img_2]: /assets/images/markdown/markdown_lists.png 'lists'
[Img_3]: /assets/images/markdown/markdown_blockquotes.png 'blockquotes'
[Img_4]: /assets/images/markdown/markdown_links_and_images.png 'links_and_images'
[Img_5]: /assets/images/markdown/markdown_emphasis.png 'emphasis'
[Img_6]: /assets/images/markdown/markdown_tables.png 'tables'
[Img_7]: /assets/images/markdown/markdown_code_blocks.png 'code_blocks'
[Img_8]: /assets/images/markdown/markdown_horizontal_rules.png 'horizontal_rules'
