---
layout: post
title: drawRect 和 layoutSubview 区别
date: 2018-01-03 09:15:00 +08:00
tags: 初学者
author: Srefan
catalog: true
tags:
    - Objective-C
    - 深阅读
---

***

### 一.概述

*  `setNeedsDisplay` 异步执行, 自动调用 `drawRect` 方法, 拿到 **UIGraphicsGetCurrentContext**, 方便视图重绘.
*  `setNeedsLayout` 异步执行, 自动调用 `layoutSubViews` 方法, 处理子视图中的一些数据, 方便数据计算, 用来调整子视图的尺寸和位置.
*  `layoutSubviews` 方法调用先于 `drawRect`.

* 程序的生命周期和代码执行顺序:

```objc
// 当一个视图控制器被创建, 并在屏幕上显示的时候.
1. alloc                   // 创建对象, 分配空间
2. init (initWithNibName)  // 初始化对象, 初始化数据
3. loadView                // 从nib载入视图, 通常这一步不需要去干涉. 除非你没有使用xib文件创建视图
4. viewDidLoad             // 载入完成, 可以进行自定义数据以及动态创建其他控件
5. viewWillAppear          // 视图将出现在屏幕之前, 马上这个视图就会被展现在屏幕上了
6. viewDidAppear           // 视图已在屏幕上渲染完成

// 当一个视图被移除屏幕并且销毁的时候的执行顺序, 这个顺序差不多和上面的相反
1. viewWillDisappear       // 视图将被从屏幕上移除之前执行
2. viewDidDisappear        // 视图已经被从屏幕上移除, 用户看不到这个视图了
3. dealloc                 // 视图被销毁, 此处需要对你在init和viewDidLoad中创建的对象进行释放

// 特殊的viewDidUnload
1. viewDidUnload           // 在发生内存警告时如果本视图不是当前屏幕上正在显示的视图的话, viewDidUnload会被执行, 本视图的所有子视图将被销毁以释放内存, 此时开发者需要手动对viewLoad,viewDidLoad中创建的对象释放内存. 当这个视图再次显示在屏幕上时, viewLoad,viewDidLoad再次被调用,以便再次构造视图.
```

上述方法的流程图可以简单用如下表示:
```
运行APP —> 载入视图 —> 调用viewDidLoad方法 —> 调用viewWillAppear方法 —> 调用viewDidAppear方法 —>   正常运行 
|                                                                                                 ∧ 
|                                                                                                 | 
|                                                                                                 | 
|                                                                                                 | 载入新的View 
|                                                                                                 | 
∨                                                                                                 | 
释放对象所有权 <— 调用viewDidUnload <— 收到内存警告 <— 调用viewDidDisappear <— 调用viewWillDisappear <—  APP需要调用另一个view 
```

### 二.drawRect

* iOS 的绘图和重绘操作是在 UIView 类的 `drawRect` 方法中完成的.
* 但是, 苹果不建议直接调用 `drawRect` 方法, 如果你强制直接调用此方法, 当然是没有效果的.
* 苹果要求我们调用 UIView 类中的 `setNeedsDisplay` 方法, 则程序会自动调用 `drawRect` 方法进行重绘.
* 重写 `drawRect: (CGRect) aRect` 方法, 此方法一般情况下只会被调用一次. 当想要手动重画这个 **View**, 只需要掉用 `[self setNeedsDisplay]` 方法.

#### 1.方法定义

```objective-c
// 重写此方法, 执行重绘任务
- (void)drawRect:(CGRect)rect;

// 标记为需要重绘, 异步调用 drawRect
- (void)setNeedsDisplay; 

// 标记为需要局部重绘
- (void)setNeedsDisplayInRect:(CGRect)rect; 
```

#### 2.调用机制

* `drawRect` 调用是在 `Controller->loadView`, `Controller->viewDidLoad` 两方法之后调用的.

1. **UIView** 初始化时**没有设置 rect 大小**, 将直接导致 `drawRect` 不被自动调用.

2. 该方法在调用 `sizeThatFits` 后被调用, 可以先调用 `sizeToFit` 计算出 **size**, 然后系统自动调用 `drawRect` 方法.

```objective-c
sizeToFit 会自动调用 sizeThatFits 方法;
sizeToFit 不应该在子类中被重写, 应该重写 sizeThatFits;
sizeThatFits 传入的参数是 receiver 当前的 size, 返回一个适合的size;
sizeToFit 可以被手动直接调用;
sizeToFit 和 sizeThatFits 方法都没有递归, 对subviews也不负责, 只负责自己.
```

* ps: 以上方式推荐; 以下方式不提倡.

3. 通过设置 `contentMode` 属性值为 **UIViewContentModeRedraw**, 那么将在每次设置或更改 **frame** 时自动调用 `drawRect`.

4. 直接调用 `setNeedsDisplay`, 或 `setNeedsDisplayInRect:` 触发 `drawRect`, 但是有个前提条件是 `rect` 不能为0.

```objective-c
-setNeedsDisplay: 标记为需要重绘, 异步调用 drawRect.
-setNeedsDisplayInRect:(CGRect)invalidRect: 标记为需要局部重绘.
```

#### 3.注意事项

* 若使用 **UIView** 绘图, 只能在 `drawRect:` 方法中获取相应的 `contextRef` 并绘图. 如果在其他方法中获取将获取到一个 **invalidate** 的 **ref** 并且不能用于画图.
* `drawRect:` 方法不能手动显示调用, 必须通过调用 `setNeedsDisplay` 或 `setNeedsDisplayInRect` 让系统自动调该方法.
* 若使用 **CALayer** 绘图, 只能在 `drawInContext:` 中(类似于 `drawRect`)绘制, 或者在 **delegate** 中的相应方法绘制. 同样也是调用 `setNeedDisplay` 等间接调用以上方法.
* 若要实时画图, 不能使用 `gestureRecognizer`, 只能使用 `touchbegan` 等方法来掉用 `setNeedsDisplay` 实时刷新屏幕.

### 三.layoutSubview

* 程序的启动顺序: viewWillAppear -> viewWillLayoutSubviews -> viewDidLayoutSubviews -> viewDidAppear
* view 中添加子控件时: viewWillLayoutSubviews -> viewDidLayoutSubviews
* `layoutSubView` 的调用顺序 (父控件 -> 本View -> 子控件), 系统会自动调用 `layoutSubviews`, 不要手动调用.

#### 1.方法定义

```objective-c
// 重写此方法, 执行重新布局任务, 默认没有做任何事情
- (void)layoutSubviews;

// 标记为需要重新布局, 异步调用 layoutIfNeeded, 不立即刷新, 但 layoutSubviews 一定会被调用
- (void)setNeedsLayout; 

// 如果有需要刷新的标记, 立即调用layoutSubviews进行布局, 如果没有标记, 不会调用layoutSubviews
- (void)layoutIfNeeded; 
```

* 如果要立即刷新, 要先调用 `[view setNeedsLayout]`, 把标记设为需要布局, 然后马上调用 `[view layoutIfNeeded]`.
* 实现布局在视图第一次显示之前, 标记总是 "需要刷新" 的, 可以直接调用 `[view layoutIfNeeded]`.

#### 2.调用机制

* `layoutSubviews` 在以下情况下会被调用:
1. **init 初始化**不会触发 layoutSubviews.
2. `addSubview` 会触发 layoutSubviews.
3. 设置 **view** 的 **frame** 会触发 layoutSubviews, 当然前提是 **frame 的值**设置前后发生了变化.
4. 滚动一个 UIScrollView 会触发 layoutSubviews.
5. 旋转 Screen 会触发父 UIView 上的 layoutSubviews 事件.
6. 改变一个 UIView 大小时也会触发父 UIView 上的 layoutSubviews 事件.
7. 直接调用 setLayoutSubviews.
8. 直接调用 setNeedsLayout.

#### 3.注意事项

* 在苹果的官方文档中强调: You should override this method only if the autoresizing behaviors of the subviews do not offer the behavior you want. 
* layoutSubviews: 当我们在某个类的内部调整子视图位置时, 需要调用. 如果你想要在外部设置 subviews 的位置, 就不要重写.

### 四.比较

* `layoutSubviews` 对 **subviews** 重新布局. `layoutSubviews` 方法调用先于 `drawRect`.
* `setNeedsLayout` 在 receiver 标上一个需要被重新布局的标记, 在系统 runloop 的下一个周期自动调用 `layoutSubviews`.
* `layoutIfNeeded` 方法如其名, UIKit 会判断该 receiver 是否需要 layout. 根据 Apple 官方文档, `layoutIfNeeded`方法应该是这样的. `layoutIfNeeded` 遍历的不是 superview 链, 是 subviews 链.
* `drawRect` 是对 receiver 的重绘, 能获得 context.
* `setNeedDisplay` 在 receiver 标上一个需要被重新绘图的标记, 在下一个 **draw 周期**自动重绘, iphone device 的刷新频率是 **60hz**, 也就是 1/60秒 后重绘.

### 五.其他

#### 1.awakeFromNib

* 当 **.nib文件** 被加载的时候, 会发送一个 awakeFromNib 的消息到 **.nib 文件**中的每个对象, 每个对象都可以定义自己的 awakeFromNib 函数来响应这个消息, 执行一些必要的操作. 
* 也就是说通过 .nib 文件创建 view 对象是执行 awakeFromNib.

#### 2.viewDidLoad

* 当 view 对象被加载到内存是就会执行 viewDidLoad, 所以不管通过 **.nib 文件**还是**代码**的方式创建对象都会执行 viewDidLoad.

#### 3.initWithNibName

* 在 controller 的类在 IB 中创建, 但是通过代码实例化 controller 的时候用的.

#### 4.initWithCoder

* initWithCoder 是一个类在 IB 中创建但通过代码实例化时被调用的.
* 通过 IB 创建一个 controller 的 .nib 文件, 然后通过代码 `initWithNibName` 来实例化 controller, 那 controller 的 `initWithCoder` 会被调用.
* 一个 view 的 .nib 文件, 类似方法创建时调用 `initWithCoder`.

#### 5.initWithNibName 和 loadNibNamed 区别

```objective-c
// ShowViewController 的 initWithNibName 方法
ShowViewController * showMessage = [[ShowViewController alloc] initWithNibName:@"ShowViewController" bundle:nil];
self.showViewController = showMessage;

// VideoCellController 的 loadNibNamed 方法
NSArray * nib = [[NSBundle mainBundle] loadNibNamed:@"SaveViewController" owner:self options:nil];
self.showViewController = [nib lastObject];
[nib objectAtIndex:0];
```

1. initWithNibName 要加载的 xib 的类为我们定义的视图控制器类.
2. 加载方式不同: 
* initWithNibName: 是延迟加载, 这个 View 上的控件是 nil 的, 只有到需要显示时才会不是 nil;
* loadNibNamed: 即时加载, 用该方法加载的 xib 对象中的各个元素都已经存在.

#### 6.initWithCoder 和 initWithFrame 区别

* initWithoder 是当从 .nib 文件中加载对象的时候会调用, 比如你的 view 来自 .nib 那么就会调用 view 的这个函数. (由框架调用).
* initWithFrame (是由用户调用, 来初始化对象的).

### 六.参考链接

此文参考于 [xiaoxiaobukuang的CSDN专栏][Link_1],[sinat_21181563的第七城市文章][Link_2],[liyubao160的CSDN博客][Link_3],[爱程序网][Link_4],[Abner的新浪博客][Link_5],十分感谢.
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: http://blog.csdn.net/xiaoxiaobukuang/article/details/51594157
[Link_2]: http://www.th7.cn/Program/IOS/201506/486828.shtml
[Link_3]: http://blog.csdn.net/u011146511/article/details/51234907
[Link_4]: http://www.aichengxu.com/ios/10303670.htm
[Link_5]: http://blog.sina.com.cn/s/blog_9c3c519b010120nk.html