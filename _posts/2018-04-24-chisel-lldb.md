---
layout: post
title: Chisel-LLDB命令插件,让调试更Easy
date: 2018-04-24 16:22:00 +08:00
tags: 转载侠
---

***

### 一.概述

* LLDB 是一个有着 REPL 的特性和 C++ ,Python 插件的开源调试器。LLDB 绑定在 Xcode 内部，存在于主窗口底部的控制台中。调试器允许你在程序运行的特定时暂停它，你可以查看变量的值，执行自定的指令，并且按照你所认为合适的步骤来操作程序的进展。([这里](http://eli.thegreenplace.net/2011/01/23/how-debuggers-work-part-1.html)有一个关于调试器如何工作的总体的解释。)

* 相信每个人或多或少都在用LLDB来调试，比如`po`一个对象。LLDB的是非常强大的，且有内建的，完整的 Python 支持。今天我们主要介绍一个 facebook 开源的 lldb 插件 Chisel。可以让你的调试更Easy.

### 二.安装Chisel

* 源码地址： [Chisel](https://github.com/facebook/chisel)

* Chisel 使用 homebrew 来安装，如果你没有安装homebrew, 参考 [homebrew](http://brew.sh/)。

```shell
brew update
brew install chisel
```
	
* 安装完成按照安装日志上的提示，在`~/.lldbinit`文件中添加一行，没有则新建。 提示类似如下：

```vim
==> Caveats
Add the following line to ~/.lldbinit to load chisel when Xcode launches:
  command script import /usr/local/opt/chisel/libexec/fblldb.py
```

* 做好上面的步骤，然后重启Xcode就可以尝试下了。

### 三.内置命令

* Chisel 为lldb提供了新增的便捷命令，是非常实用的命令.

#### 3.1 pviews

* 这个命令可以递归打印所有的view，并能标示层级，相当于 UIView 的私有辅助方法 `[view recursiveDescription]` 。 善用使用这个功能会让你在调试定位问题时省去很多麻烦。

* 使用示例：

```shell
(lldb) pviews view
<TestView: 0x18df8070; baseClass = UIControl; frame = (144 9; 126 167); layer = <CALayer: 0x18df8150>>
   | <UIView: 0x18df81d0; frame = (0 0; 126 126); userInteractionEnabled = NO; layer = <CALayer: 0x18df8240>>
   | <UIImageView: 0x18df8330; frame = (0 0; 126 126); clipsToBounds = YES; opaque = NO; userInteractionEnabled = NO; layer = <CALayer: 0x18df83b0>>
   | <UILabel: 0x18df8460; frame = (0 135; 126 14); text = 'haha'; userInteractionEnabled = NO; layer = <_UILabelLayer: 0x18df7fb0>>
   |    | <_UILabelContentLayer: 0x131a3d50> (layer)
   | <UILabel: 0x18df8670; frame = (0 155; 126 12); text = 'hahaha'; userInteractionEnabled = NO; layer = <_UILabelLayer: 0x18df8730>>
   |    | <_UILabelContentLayer: 0x131bea10> (layer)
   | <UIImageView: 0x18df88d0; frame = (0 9; 28 27); hidden = YES; opaque = NO; userInteractionEnabled = NO; layer = <CALayer: 0x18df8ba0>>
```

#### 3.2 pvc
* 这个命令也是递归打印层级，但是不是view，而是viewController。利用它我们可以对viewController的结构一目了然。 其实苹果在IOS8也默默的添加了 UIViewController 的一个私有辅助方法 `[UIViewController _printHierarchy]` 同样的效果。

* 预览效果:

```shell
(lldb) pvc
<TabBarController: 0x13772fd0; view = <UILayoutContainerView; 0x151b3a30>; frame = (0, 0; 414, 736)>
   | <UINavigationController: 0x1602b800; view = <UILayoutContainerView; 0x1b00aca0>; frame = (0, 0; 414, 736)>
   |   | <FirstViewController: 0x16029c00; view = <UIView; 0x1b01e1c0>; frame = (0, 0; 414, 736)>
   | <UINavigationController: 0x138c5200; view = <UILayoutContainerView; 0x1316a080>; frame = (0, 0; 414, 736)>
   |   | <SecondViewController: 0x16030400; view = <UIView; 0x2094b370>; frame = (0, 0; 414, 736)>
   |   |   | <SecondChildViewController: 0x15af6000; view = <UIView; 0x18d4e650>; frame = (0, 64; 414, 628)>
   | <UINavigationController: 0x1383ca00; view = <UILayoutContainerView; 0x13180070>; frame = (0, 0; 414, 736)>
   |   | <ThirdViewController: 0x138ddc00; view = <UIView; 0x18df6650>; frame = (0, 0; 414, 736)>
   |   |   | <ThirdChild1ViewController: 0x1393fe00; view = <UIView; 0x131ec000>; frame = (0, 0; 414, 672)>
   |   |   | <ThirdChild2ViewController: 0x138dce00; view = <UIView; 0x204075a0>; frame = (414, 0; 414, 672)>
   |   |   | <ThirdChild3ViewController: 0x138a8e00; view = <UIView; 0x20426250>; frame = (828, 0; 414, 672)>
   | <UINavigationController: 0x160eca00; view = <UILayoutContainerView; 0x152f7d90>; frame = (0, 0; 414, 736)>
   |   | <FourViewController: 0x13157cc0; view not loaded>
```

* 是不是方便很多呢，而且还可以看到 viewController 是否已经 viewDidLoad.

#### 3.3 visualize

* 这是个很有意思的功能，它可以让你使用Mac的预览打开一个 UIImage, CGImageRef, UIView, 或 CALayer。 这个功能或许可以帮我们用来截图、用来定位一个view的具体内容。 但是在我试用了一下，发现暂时还是只能在模拟器时使用，真机还不行。

* 使用简单:

```shell
(lldb) visualize imageView
```

#### 3.4 fv & fvc

* `fv` 和 `fvc` 这两个命令是用来通过类名搜索当前内存中存在的view和viewController实例的命令，支持正则搜索。

* 如：

```shell
(lldb) fv scrollView
0x18d3b8c0 UIScrollView
0x137d0c50 UIScrollView
0x131b1580 UIScrollView
0x131b2070 UIScrollView
(lldb) fvc Home
0x1393fe00 HomeFeedsViewController
0x138a8e00 HomeFeedsViewController
```

#### 3.5 show & hide

* 这两个命令用来显示和隐藏一个指定的 UIView . 你甚至不需要Continue Progress. 就可以看到效果。

#### 3.6 mask/umask border/unborder

* 这两组命令用来标识一个view或layer的位置时用， mask用来在view上覆盖一个半透明的矩形， border可以给view添加边框。但是在我实际使用的过程中mask总是会报错，估计是有bug， 那么mask/unmask 一般不要用好了，用border命令是一样的效果，反正二者的用途都是找到一个对应的view.

#### 3.7 caflush
* 这个命令会重新渲染，即可以重新绘制界面， 相当于执行了 `[CATransaction flush]` 方法，要注意如果在动画过程中执行这个命令，就直接渲染出动画结束的效果。

* 当你想在调试界面颜色、坐标之类的时候，可以直接在控制台修改属性，然后`caflush`就可以看到效果啦，是不是要比改代码，然后重新build省事多了呢。

* 例, 其中 $122 即是目标UIView：

```shell
(lldb) p view
(long) $122 = 140718754142192
(lldb) e (void)[$122 setBackgroundColor:[UIColor greenColor]]
(lldb) caflush
```

#### 3.8 bmessage

* 这个命令就是用来打断点用的了，虽然大家断点可能都喜欢在图形界面里面打，但是考虑一种情况：我们想在 `[MyViewController viewWillAppear:]` 里面打断点，但是 MyViewController并没有实现 `viewWillAppear:` 方法， 以往的作法可能就是在子类中实现下`viewWillAppear:`，然后打断点，然后rebuild。

* 那么幸好有了 `bmessage` 命令。我们可以不用这样就可以打这个效果的断点： `(lldb) bmessage -[MyViewController viewWillAppear:]` 上面命令会在其父类的 `viewWillAppear:` 方法中打断点，并添加上了条件：`[self isKindOfClass:[MyViewController class]]`

### 四.自定义命令

* 我们也可以自定义插件，不过前提是要懂一些 python。 比如设计一个打印keyWindow的windowLevel的命令：

* 创建python脚本文件 `/magical/commands/example.py` :

```shell
#!/usr/bin/python
# Example file with custom commands, located at /magical/commands/example.py

import lldb
import fblldbbase as fb

def lldbcommands():
  return [ PrintKeyWindowLevel() ]

class PrintKeyWindowLevel(fb.FBCommand):
  def name(self):
    return 'pkeywinlevel'

  def description(self):
    return 'An incredibly contrived command that prints the window level of the key window.'

  def run(self, arguments, options):
    # It's a good habit to explicitly cast the type of all return
    # values and arguments. LLDB can't always find them on its own.
    lldb.debugger.HandleCommand('p (CGFloat)[(id)[(id)[UIApplication sharedApplication] keyWindow] windowLevel]')
```

* 其中定义了`PrintKeyWindowLevel`的类，需要实现 `name` `description` `run` 方法来分别告诉名称、描述、和执行实体。

* 创建好脚本后，然后在前面安装时创建的 `~/.lldbinit` 文件中添加一行：

```shell
script fblldb.loadCommandsInDirectory('/magical/commands/')
```

* 然后重启Xcode之后就可以使用自定义的命令啦。

### 五.参考链接

此文参考于 [刘坤的技术博客][Link_1],十分感谢.  
原文参考文献:[Chisel官方说明](https://github.com/facebook/chisel/blob/master/README.md),[与调试器共舞 – LLDB 的华尔兹](http://objccn.io/issue-19-2/).   
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: https://blog.cnbluebox.com/blog/2015/03/05/chisel/