---
layout: post
title: Markdown 语法入门
date: 2016-12-09 16:12:00 +08:00
tags: 初学者
---

***
## 一.概述

**Markdown** 是一种轻量级标记语言,由 John Gruber 设计,目标是实现 **[易读易写]** . 本文参考 [markdown.cn](http://www.markdown.cn) 和 [MacDown Software](http://macdown.uranusjr.com/download/latest/) 的帮助文档.

***

## 二.规则
### 1.标题
Markdown 支持两种标题的语法,类 Setext 和类 atx 形式.常用 atx 形式,写法如下:

> \# 一级标题<br/>
> \## 二级标题<br/>
> \### 三级标题

以此类推,总共六级标题,建议在 **#** 号后加一个 **空格** ,这是最标准的 Markdown 语法.

![标题][Img_1]

### 2.列表
#### 有序列表
有序列表用 **数字+英文句点** 的方式:

> 1\. Rad<br/>
> 2\. Green<br/>
> 3\. Blue

#### 无序列表
无序列表用 **\*** 或 **\+** 或 **\-** 的方式 (三个效果是一样的) :

> \* Red<br/>
> \* Green<br/>
> \* Blue

![列表][Img_2]

### 3.引用
引用别处的句子用引用的格式,每行文本前加 **>** :

> \> 这是一段引用

![引用][Img_3]

### 4.链接和图片
链接和图片语法接近,区别在于开头位置的 **\!** :

> **[** _Google_ **]** **(** _https://www.google.com/_ **)** <br/>
> **!** **[** _Sublime Text_ **]** **(** _http://yangbryant.github.io/assets/images/favicon.png_ **)**

![链接和图片][Img_4]

### 5.粗体和斜体
斜体用 **一个\*** 或 **一个\_** 包含, 粗体用 **两个\*** 或 **两个\_** 包含 的方式 (* 和 _ 效果是一样的) :

> \*\*这是粗体写法\*\*<br/>
> \*这是斜体写法\*

![粗体和斜体][Img_5]

### 6.表格





*重点***更重点**_非重点___无关紧要__

```c
printf("Hello world!\n");
```
\*literal asterisks\*

<http://example.com/>

<address@example.com>

![图片][1]

You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

this is a code: `prinf("Hello world!\n")`

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[Img_1]: /assets/images/markdown/markdown_title.png 'title'
[Img_2]: /assets/images/markdown/markdown_list.png 'list'
[Img_3]: /assets/images/markdown/markdown_quote.png 'quote'
[Img_4]: /assets/images/markdown/markdown_link_and_pic.png 'link_and_pic'
[Img_5]: /assets/images/markdown/markdown_font.png 'font'

[1]: /assets/images/sublime-text-cheat-sheet.jpg 'sublime'
[jekyll-docs]: http://jekyllrb.com/docs/home 'docs'
[jekyll-gh]:   https://github.com/jekyll/jekyll 'gh'
[jekyll-talk]: https://talk.jekyllrb.com/ 'talk'
