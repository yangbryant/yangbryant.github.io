---
layout: post
title: pragma与Objective-C的警告
date: 2018-01-04 16:15:00 +08:00
author: Srefan
catalog: true
tags:
    - Objective-C
    - 深阅读
---

***

### 一.概述

* `#pragma` 声明是彰显 Objective-C 工艺的标志之一. 虽然 `#pragma` 最初的目的是为了使得源代码在不同的编译器下兼容的, 但精明的Xcode编码器将 `#pragma` 使用到了极致.
* 在现在的背景下, `#pragma` 避开了注释和代码之间的界限. 作为预处理指令, `#pragma` 在编译时进行计算. 但它并不像如 `#ifdef...#endif` 之类的宏, `#pragma` 的使用方式不会改变你的应用运行时的行为. 相反的, `#pragma` 声明主要由 Xcode 用来完成两个主要任务: 整理代码和防止编译器警告.
* ObjC 由于现在由苹果负责维护, Clang 的 LLVM 也同时是苹果在做, 可以说从语言到编译器到 SDK 全局都在掌握之中, 因此做 ObjC 开发时的警告往往比其他语言的警告更有参考价值. 打开尽可能多的警告提示, 并且在程序开发中尽量避免生成警告, 对于构建一个健壮高效的程序来说, 是必须的.

### 二.应用场景

#### 整理你的代码

* 代码整理是一个卫生的问题.
* 你如何组织你的代码反映的是你和你的工作. 缺少惯性和内部一致性的代码表明要么有疏忽要么无能. 更糟的是, 使得一个项目难以维持和协作.

* 好的习惯从`#pragma mark`开始. 就像这样:

```objective-c
@implementation ViewController
- (id)init {
  ...
}

#pragma mark - UIViewController
- (void)viewDidLoad {
  ...
}

#pragma mark - IBAction
- (IBAction)cancel:(id)sender {
  ...
}

#pragma mark - UITableViewDataSource
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
  ...
}

#pragma mark - UITableViewDelegate
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
  ...
}
```

* 在你的 `@implementation` 中使用 `#pragma mark` 来将代码分割成逻辑区块. 这些逻辑区块不仅仅使得阅读代码本身容易许多, 也为Xcode源导航增加了视觉线索 ( `#pragma mark` 声明前有一个水平分割并由破折号 `－` 开始).

![Xcode Sections][Image_1]

* 如果你的类符合任何 `@protocols`, 先将各协议的方法放在一起, 并添加 `#pragma mark` 以及协议的名称. 另一个好的惯例是将各子类方法根据它们的超类组合放在一起. 比如, 一个 `NSInputStream` 子类应该放在 `NSStream` 的组里. 如 `IBAction` 的出口, 或者对应于目标／动作, 通知, 或者 KVO 选择器之类的方法也值得拥有它们各自的区块.

* 你的代码应该干净到你可以吃掉它. 所以花点时间整理你的 `.m` 文件让它们比你生成它们时还干净.

#### 防止警告

* `#pragma mark`十分主流. 另一方面, 用 `#pragma` 声明来防止来自编译器或者静态分析器的警告现在还是很新鲜的.
* 你知道有什么是比有糟糕的格式的代码更烦人的吗? 生成警告的代码. 尤其是第三方的代码. 一个供应库要花很长时间来编译, 终于成功编译时却生成了200+警告这种事实在很烦人. 即使传送有一个警告的代码也很不好.

> 资深提示: 尝试设置 `-Weverything` 标志, 并在你的 build setting 里选择 **"Treat Warnings as Errors"**. 这将会开启Xcode的困难模式.

* 可是有的时候并没有办法避免编译器警告. 弃用通知和保留周期误报是这个情况可能发生的常见情况. 在这些罕见的, 你确认一个特定的编译器或者稳态分析器警告需要被抑制时, 可以通过**临时改变诊断编译标记**来抑制指定类型的警告, `#pragma` 就派上用场了:

```objective-c
// completionBlock在AFURLConnectionOperation中被手动的设置为nil来打破保留周期.
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Warc-retain-cycles"
    self.completionBlock = ^ {
        ...
    };
#pragma clang diagnostic pop
```

* <del>这个[来自于AFNetworking](https://github.com/AFNetworking/AFNetworking/blob/master/AFNetworking/AFHTTPRequestOperation.m#L247) (contributed by [Peter Steinberger](https://github.com/steipete))的代码是一个不可避免的静态分析器警告的例子. Clang 注意到块中指向 `self` 的强引用, 并警告可能的[保留周期](http://www.quora.com/What-is-a-retain-cycle). 然而, `setCompletionBlock` 的 `super` 实现通过在块结束时将强引用设置为 `nil` 来解决这个问题.</del> [^Footnote_1]
* 幸运的是, Clang提供了一个方便的方法来解决这一切. 通过使用`#pragma clang diagnostic push/pop`, 你可以告诉编译器**仅仅**为某一特定部分的代码 (最初的诊断设置在最后的 `pop` 被恢复) 来忽视特定警告.

* ps: 其他警告忽略情况参考<a href="#pragma_clang_diagnostic_ignored">这里</a>.


> 你可以在[Clang Compiler User's Manual](http://clang.llvm.org/docs/UsersManual.html#diagnostics_pragmas)读到更多关于 `#pragma` 的 LLVM 用法的知识.

* 只是不要用这种方法来清扫地毯下的合法的警告 -- 这将会给你带来麻烦.

#### 加入警告

* Clang 不仅提供了我们暂时关闭警告的办法, 也提供了加入警告的办法.
* 强制加入一个警告:

```objective-c
//Generate a warning
#pragma message "Warning 1"

//Another way to generate a warning
#warning "Warning 2"
```

* 两种强制警告的方法在视觉效果上结果是一样的, 但是警告类型略有不同, 一个是 `-W#pragma-messages`, 另一个是 `-W#warnings`.
* 对于第二种写法, 把 warning 换成 error, 可以强制使编译失败. 比如在发布一些需要 API Key 之类的类库时, 可以使用这个方法来提示别的开发者别忘了输入必要的信息.

```objective-c
//Generate an error to fail the build.
#error "Something wrong"
```

#### 全局关闭警告

* 如果需要全局关闭的话, 直接在 `Other C Flags` 里写 `-Wno-...` 就行了.
* 比如 `-Wextra -Wno-sign-compare` 就是一个常见的组合.
* 如果相对某几个文件开启或禁用警告, 在 Build Phases 的 `Compile Source` 相应的文件中加入对应的编译标识即可.

#### 在Xcode中开启额外警告提示

* Xcode 的工程模板已经为我们设置开启了一些默认和常用的警告提示, 这些默认设置为了兼容一些上年头的项目, 并没有打开很多, 仅是指对最危险和最常见的部分进行了警告. 这对于一个新项目来说这是不够用的, 在无数前辈大牛的教导下, 首先要做的事情就是打开尽可能多的警告提示.

##### 通过UI来打开警告 -- 最简单的方法

* 在 Xcode 中, Build Setting 选项里为我们预留了一些打开警告的开关, 找到并直接勾选相应的选项就可以打开警告. 
* 大部分时间里选项本身已经足够能描述警告的作用和产生警告的时机, 如果不是很明白的话, 在右侧的 Quick Help 面板里有更详细的说明. 对于OC开发来说特有的警告都在 `Apple LLVM compiler 4.2 - Warnings - Objective C` 一栏中, 不管您是不是决定打开它们, 都是值得花时间看一看加以了解的, 因为它们都是写OC程序时最应该避免的情况.
* 另外几个 `Apple LLVM compiler 4.2 - Warnings - …(All languages和C++)` 也包含了大量的选项, 以方便控制警告产生.

![xcode-warning][Image_2]

##### 编译选项参数打开警告
* 在编译选项中加入合适的 flag 能够打开或者关闭警告, 在 Build Setting 中的 `Other C Flags` 里添加形似 `-W...` 的编译标识. 你可以在其中填写任意多的 `-W...` 以开关某些警告. 
* 比如, 填写为 `-Wall -Wno-unused-variable` 即可打开"全部"警告 (其实并不是全部, 只是一大部分严重警告而已, 详细请继续读下去). 但是不启用"未使用变量"的警告. 
* 使用 `-W...` 的形式, 而不是在UI上勾选的一大好处是, 在编译器版本更新时, 新加入的警告如果包含在 `-Wall` 中的话, 不需要对工程做任何修改, 新的警告即可以生效. 这样立即可以察觉到同一个工程由于编译器版本更新时可能带来的隐患.
* 另外一个更重要的原因是 Xcode 的UI并没有提供所有的警告.

> 需要注意的是, `-Wall` 的名字虽然是 all, 但是这真的只是一个迷惑人的词语, 实际上 `-Wall` 涵盖的仅只是所有警告中的一个子集. 在 **StackExchange** 上有一个在Google工作的Clang开发者进行的回答,其中解释了有一些重要的警告组:
> 
> * `-Wall` 并不是 **所有警告**.
> 这一个警告组开启的是编译器开发者对于"你所写的代码中有问题"这一命题有着**很高的自信**的那些警告. 要是在这一组设定下你的代码出现了警告, 那基本上就是你的代码真的存在严重问题了.
> 但是同时, 并不是说打开 -Wall 就万事大吉了, 因为 -Wall 所针对的仅仅只是经典代码库中的为数不多的问题, 因此有一些致命的警告并不能被其捕捉到.
> 但是不论如何，因为 -Wall 的警告提供的都是可信度和优先级很高的警告, 所以为所有项目 (至少是所有新项目) 打开这组警告, 应该成为一种良好的习惯.
> 
> * `-Wextra` 如其所名, -Wextra 组提供"额外的"警告. 
> 这个组和 -Wall 组几乎一样有用, 但是有些情况下对于代码相对过于严苛. 
> 一个很常见的例子是, -Wextra 中包含了 `-Wsign-compare`, 这个警告标识会开启比较时候对 `signed` 和 `unsigned` 的类型检查, 当比较符两边一边是 `signed` 一边是 `unsigned` 时, 产生警告. 其实很多代码并没有特别在意这样的比较, 而且绝大多数时候, 比较 `signed` 和 `unsigned` 也是没有太大问题的 (当然不排除会有致命错误出现的情况). 
> 需要注意, `-Wextra` 和 `-Wall` 是相互独立的两个警告组, 虽然里面打开的警告标识有个别是重复的, 但是两组并没有包含的关系. 想要同时使用的话必须在 `Other C Flags` 中都加上.
> 
> * `-Weverything` 这个是**真正的所有警告**. 这就是前面**资深提示讲的困难模式**.
> 但是一般开发者不会选择使用这个标识, 因为它包含了那些还正在开发中的可能尚存bug的警告提示. 
> 这个标识一般是编译器开发者用来调试时使用的, 如果你想在自己的项目里开启的话, 警告一定会爆棚导致你想开始撞墙.
> 
> * `-Wall` 和 `-Wextra` 下0警告的工程, 在 `-Weverything` 下的表现, 可以用惨不忍睹来形容.
> * 关于某个组开启了哪些警告的说明, 在 GCC 的手册中有[一个参考](http://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html). 虽然苹果现在用的都是 `LLVM` 了, 但是这部分内容应该是继承了 `GCC` 的设定.

![weverything][Image_3]

#### 我应该开启哪些警告提示

* 个人喜好(代码洁癖)不同, 会有不同的需求. 建议是对于所有项目, 特别是新开的项目, 首先开启 `-Wall` 和 `-Wextra`, 然后在此基础上构建项目并且避免一切警告.
* 如果在开发过程中遇到了某些确实无法解决或者确信自己的做法是正确的话 (其实这种情况, 你的做法一般即使不是错误的, 也会是不那么正确的), 可以有选择性地关闭某些警告.
* 一般来说, 关闭的警告数不应该超过一只手能数出来的数字, 否则一定哪儿出问题了...

#### 是否要让警告等于错误

* 一种很常见的做法和代码洁癖是将警告标识为错误, 从而中断编译过程. 这让开发者不得不去修复这些警告, 从而保持代码干净整洁. 在 Xcode 中, 可以通过勾选相应的 `Treat Warnings as Errors` 来开启, 或者加入 `-Werror` 标识.
* 个人来说不喜欢使用这个设定, 因为它总是打断开发流程. 很多时候并不可能把代码全写完再编译调试, 相反地, 我更喜欢写一点就编译运行一下看看结果, 这样在中间 debug 编译的时候会出现警告也不足为奇.
* 另外, 如果做 TDD 开发时, 也可能会有大量正常的警告出现, 如果有警告就不让编译的话, 开发效率可能会打折扣.
* 一个比较好的做法是只在 `Release Build` 时将警告视为错误, 因为 Xcode 中是可以为 Debug 和 Release 分别指定标识的, 所以这很容易做到.
* 另外也可以只把某些警告当作错误, `-Werror=...` 即可, 同样地, 也可以在 `-Werror` 被激活时使用 `-Wno-error=...` 来使某些警告不成为错误. 结合使用这些编译标识可以达到很好的控制.

### 三.<a name="pragma_clang_diagnostic_ignored">让编译器对警告闭嘴</a>

#### 1.方法废弃警告

```objective-c
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wdeprecated-declarations"
//code这里插入相关的代码
#pragma clang diagnostic pop
```

#### 2.不兼容指针类型警告

```objective-c
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wincompatible-pointer-types"
//code这里插入相关的代码
#pragma clang diagnostic pop
```

#### 3.循环引用警告

```objective-c
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Warc-retain-cycles"
//code这里插入相关的代码
#pragma clang diagnostic pop
```

#### 4.未使用变量警告

```objective-c
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wunused-variable"
//code这里插入相关的代码
#pragma clang diagnostic pop
```

#### 5.selector中使用了不存在的方法名

* 在使用反射机制通过类名创建类对象的时候会需要的

```objective-c
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wundeclared-selector"
//code这里插入相关的代码
#pragma clang diagnostic pop
```

#### 完整列表参照<a href="#pragma_clang_detail_table">这里</a>.

### 四.参考链接

此文参考于 [NSHipster][Link_1][中文站][Link_2], [喵神的博客][Link_3], 十分感谢.  
所有引用内容版权归原作者所有.  
全面的 Clang 警告综合列表来源于[F***ingClangWarnings.com][Link_4].  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: http://nshipster.cn/pragma/
[Link_2]: http://nshipster.cn/clang-diagnostics/
[Link_3]: https://onevcat.com/2013/05/talk-about-warning/
[Link_4]: http://fuckingclangwarnings.com/

[Image_1]: /assets/images/pragma/pragma-xcode-sections.png "pragma-xcode-sections"
[Image_2]: /assets/images/pragma/xcode-warning.png "xcode-warning"
[Image_3]: /assets/images/pragma/weverything.png "weverything"

[^Footnote_1]: 在 AFNetworking 3.0 版本中 AFHTTPRequestOperation 已经被废弃.

***

### 五.<a name="pragma_clang_detail_table">附表</a>


<style>
table th:first-of-type {
    width: 200px;
}
table th:nth-of-type(2) {
}
</style>


##### **Semantic Warnings**

| Warning | Message |
|:---------- |:---------- |
| -WCFString-literal | 	input conversion stopped due to an input byte that does not belong to the input codeset UTF-8 |
| -WNSObject-attribute | \_\_attribute ((NSObject)) may be put on a typedef only, attribute is ignored |
| -Wabstract-vbase-init | initializer for virtual base class %0 of abstract class %1 will never be used |
| -Waddress-of-array-temporary | pointer is initialized by a temporary array, which will be destroyed at the end of the full-expression |
| -Warc-maybe-repeated-use-of-weak | weak %select{variable\|property\|implicit property\|instance variable}0 %1 may be accessed multiple times in this %select{function\|method\|block\|lambda}2 and may be unpredictably set to nil assign to a strong variable to keep the object alive |
| -Warc-non-pod-memaccess | %select{destination for\|source of}0 this %1 call is a pointer to ownership-qualified type %2 |
| -Warc-performSelector-leaks | performSelector may cause a leak because its selector is unknown |
| -Warc-repeated-use-of-weak | weak %select{variable\|property\|implicit property\|instance variable}0 %1 is accessed multiple times in this %select{function\|method\|block\|lambda}2 but may be unpredictably set to nil assign to a strong variable to keep the object alive |
| -Warc-retain-cycles | capturing %0 strongly in this block is likely to lead to a retain cycle |
| -Warc-unsafe-retained-assign | assigning retained object to unsafe property object will be released after assignment |
| -Warc-unsafe-retained-assign | assigning %select{array literal\|dictionary literal\|numeric literal\|boxed expression\|should not happen\|block literal}0 to a weak %select{property\|variable}1 object will be released after assignment |
| -Warc-unsafe-retained-assign | assigning retained object to %select{weak\|unsafe_unretained}0 %select{property\|variable}1 object will be released after assignment |
| -Warray-bounds | array index %0 is past the end of the array (which contains %1 element%s2) |
| -Warray-bounds | array index %0 is before the beginning of the array |
| -Warray-bounds | 'static' has no effect on zero-length arrays |
| -Warray-bounds | array argument is too small contains %0 elements, callee requires at least %1 |
| -Warray-bounds-pointer-arithmetic | the pointer incremented by %0 refers past the end of the array (that contains %1 element%s2) |
| -Warray-bounds-pointer-arithmetic | the pointer decremented by %0 refers before the beginning of the array |
| -Wassign-enum | integer constant not in range of enumerated type %0 |
| -Watomic-property-with-user-defined-accessor | writable atomic property %0 cannot pair a synthesized %select{getter\|setter}1 with a user defined %select{getter\|setter}2 |
| -Wattributes | unknown attribute %0 ignored |
| -Wauto-var-id | 'auto' deduced as 'id' in declaration of %0 |
| -Wavailability | unknown platform %0 in availability macro |
| -Wavailability | overriding method %select{introduced after\|deprecated before\|obsoleted before}0 overridden method on %1 (%2 vs. %3) |
| -Wavailability | availability does not match previous declaration |
| -Wavailability | overriding method cannot be unavailable on %0 when its overridden method is available |
| -Wavailability | feature cannot be %select{introduced\|deprecated\|obsoleted}0 in %1 version %2 before it was %select{introduced\|deprecated\|obsoleted}3 in version %4 attribute ignored |
| -Wbad-function-cast | cast from function call of type %0 to non-matching type %1 |
| -Wbitfield-constant-conversion | implicit truncation from %2 to bitfield changes value from %0 to %1 |
| -Wbitwise-op-parentheses | '&' within '\|' |
| -Wbool-conversion | "initialization of pointer of type %0 to null from a constant boolean " "expression |
| -Wbridge-cast | %0 cannot bridge to %1 |
| -Wbridge-cast | %0 bridges to %1, not %2 |
| -Wbuiltin-requires-header | declaration of built-in function '%0' requires inclusion of the header {stdio.h\|setjmp.h\| ucontext.h} |
| -Wc++-compat | %select{\|empty }0%select{struct\|union}1 has size 0 in C, %select{size 1\|non-zero size}2 in C++ |
| -Wc++11-compat | explicit instantiation cannot be 'inline' |
| -Wc++11-compat | explicit instantiation of %0 must occur at global scope |
| -Wc++11-compat | explicit instantiation of %0 not in a namespace enclosing %1 |
| -Wc++11-compat | explicit instantiation of %q0 must occur in namespace %1 |
| -Wc++11-narrowing | constant expression evaluates to %0 which cannot be narrowed to type %1 in C++11 |
| -Wc++11-narrowing | type %0 cannot be narrowed to %1 in initializer list in C++11 |
| -Wc++11-narrowing | non-constant-expression cannot be narrowed from type %0 to %1 in initializer list in C++11 |
| -Wc++98-c++11-compat | type definition in a constexpr %select{function\|constructor}0 is incompatible with C++ standards before C++1y |
| -Wc++98-c++11-compat | use of this statement in a constexpr %select{function\|constructor}0 is incompatible with C++ standards before C++1y |
| -Wc++98-c++11-compat | init-captures.def warn_cxx11_compat_init_capture : Warning "initialized lambda captures are incompatible with C++ standards " "before C++1y |
| -Wc++98-c++11-compat | variable declaration in a constexpr %select{function\|constructor}0 is incompatible with C++ standards before C++1y |
| -Wc++98-c++11-compat | constexpr function with no return statements is incompatible with C++ standards before C++1y |
| -Wc++98-c++11-compat | multiple return statements in constexpr function is incompatible with C++ standards before C++1y |
| -Wc++98-c++11-compat | variable templates are incompatible with C++ standards before C++1y |
| -Wc++98-compat | substitution failure due to access control is incompatible with C++98 |
| -Wc++98-compat | %select{anonymous struct\|union}0 member %1 with a non-trivial %select{constructor\|copy constructor\|move constructor\|copy assignment operator\|move assignment operator\|destructor}2 is incompatible with C++98 |
| -Wc++98-compat | enumeration type in nested name specifier is incompatible with C++98 |
| -Wc++98-compat | static data member %0 in union is incompatible with C++98 |
| -Wc++98-compat | default template arguments for a function template are incompatible with C++98 |
| -Wc++98-compat | scalar initialized from empty initializer list is incompatible with C++98 |
| -Wc++98-compat | befriending %1 without '%select{struct\|interface\|union\|class\|enum}0' keyword is incompatible with C++98 |
| -Wc++98-compat | use of null pointer as non-type template argument is incompatible with C++98 |
| -Wc++98-compat | friend declaration naming a member of the declaring class is incompatible with C++98 |
| -Wc++98-compat | non-class friend type %0 is incompatible with C++98 |
| -Wc++98-compat | befriending enumeration type %0 is incompatible with C++98 |
| -Wc++98-compat | use of non-static data member %0 in an unevaluated context is incompatible with C++98 |
| -Wc++98-compat | friend function %0 would be implicitly redefined in C++98 |
| -Wc++98-compat | %select{class template\|class template partial\|variable template\|variable template partial\|function template\|member function\|static data member\|member class\|member enumeration}0 specialization of %1 outside namespace %2 is incompatible with C++98 |
| -Wc++98-compat | reference initialized from initializer list is incompatible with C++98 |
| -Wc++98-compat | redundant parentheses surrounding address non-type template argument are incompatible with C++98 |
| -Wc++98-compat | initialization of initializer_list object is incompatible with C++98 |
| -Wc++98-compat | use of 'template' keyword outside of a template is incompatible with C++98 |
| -Wc++98-compat | non-type template argument referring to %select{function\|object}0 %1 with internal linkage is incompatible with C++98 |
| -Wc++98-compat | use of 'typename' outside of a template is incompatible with C++98 |
| -Wc++98-compat | passing object of trivial but non-POD type %0 through variadic %select{function\|block\|method\|constructor}1 is incompatible with C++98 |
| -Wc++98-compat | goto would jump into protected scope in C++98 |
| -Wc++98-compat | constructor call from initializer list is incompatible with C++98 |
| -Wc++98-compat | 'auto' type specifier is incompatible with C++98 |
| -Wc++98-compat | delegating constructors are incompatible with C++98 |
| -Wc++98-compat | 'constexpr' specifier is incompatible with C++98 |
| -Wc++98-compat | inheriting constructors are incompatible with C++98 |
| -Wc++98-compat | explicit conversion functions are incompatible with C++98 |
| -Wc++98-compat | switch case would be in a protected scope in C++98 |
| -Wc++98-compat | '%0' type specifier is incompatible with C++98 |
| -Wc++98-compat | indirect goto might cross protected scopes in C++98 |
| -Wc++98-compat-pedantic | cast between pointer-to-function and pointer-to-object is incompatible with C++98 |
| -Wc++98-compat-pedantic | implicit conversion from array size expression of type %0 to %select{integral\|enumeration}1 type %2 is incompatible with C++98 |
| -Wcast-align | cast from %0 to %1 increases required alignment from %2 to %3 |
| -Wcast-of-sel-type | cast of type %0 to %1 is deprecated use sel_getName instead |
| -Wchar-subscripts | array subscript is of type 'char' |
| -Wconditional-uninitialized | variable %0 may be uninitialized when %select{used here\|captured by block}1 |
| -Wconstant-logical-operand | use of logical '%0' with constant operand |
| -Wconstexpr-not-const | 'constexpr' non-static member function will not be implicitly 'const' in C++1y add 'const' to avoid a change in behavior |
| -Wconsumed | state of variable '%0' must match at the entry and exit of loop |
| -Wconsumed | parameter '%0' not in expected state when the function returns: expected '%1', observed '%2' |
| -Wconsumed | argument not in expected state expected '%0', observed '%1' |
| -Wconsumed | invalid invocation of method '%0' on a temporary object while it is in the '%1' state |
| -Wconsumed | return state set for an unconsumable type '%0' |
| -Wconsumed | consumed analysis attribute is attached to member of class '%0' which isn't marked as consumable |
| -Wconsumed | invalid invocation of method '%0' on object '%1' while it is in the '%2' state |
| -Wconsumed | return value not in expected state expected '%0', observed '%1' |
| -Wconversion | implicit conversion discards imaginary component: %0 to %1 |
| -Wconversion | non-type template argument with value '%0' converted to '%1' for unsigned template parameter of type %2 |
| -Wconversion | implicit conversion loses floating-point precision: %0 to %1 |
| -Wconversion | implicit conversion loses integer precision: %0 to %1 |
| -Wconversion | non-type template argument value '%0' truncated to '%1' for template parameter of type %2 |
| -Wconversion | implicit conversion turns vector to scalar: %0 to %1 |
| -Wconversion | implicit conversion turns floating-point number into integer: %0 to %1 |
| -Wcovered-switch-default | default label in switch which covers all enumeration values |
| -Wcustom-atomic-propertiesatomic | by default property %0 has a user defined %select{getter\|setter}1 (property should be marked 'atomic' if this is intended) |
| -Wdangling-field | initializing pointer member %0 with the stack address of parameter %1 |
| -Wdangling-field | binding reference %select{\|subobject of }1member %0 to a temporary value |
| -Wdangling-field | binding reference member %0 to stack allocated parameter %1 |
| -Wdangling-initializer-list | array backing the initializer list will be destroyed at the end of %select{the full-expression\|the constructor}0 |
| -Wdelete-incomplete | deleting pointer to incomplete type %0 may cause undefined behavior |
| -Wdelete-non-virtual-dtor | delete called on %0 that is abstract but has non-virtual destructor |
| -Wdelete-non-virtual-dtor | delete called on %0 that has virtual functions but non-virtual destructor |
| -Wdeprecated | access declarations are deprecated use using declarations instead |
| -Wdeprecated | definition of implicit copy %select{constructor\|assignment operator}1 for %0 is deprecated because it has a user-declared %select{copy %select{assignment operator\|constructor}1\|destructor}2 |
| -Wdeprecated | dynamic exception specifications are deprecated |
| -Wdeprecated-increment-bool | incrementing expression of type bool is deprecated |
| -Wdeprecated-objc-isa-usage | assignment to Objective-C's isa is deprecated in favor of object_setClass() |
| -Wdeprecated-objc-isa-usage | direct access to Objective-C's isa is deprecated in favor of object_getClass() |
| -Wdeprecated-objc-pointer-introspection | bitmasking for introspection of Objective-C object pointers is strongly discouraged |
| -Wdeprecated-objc-pointer-introspection-performSelector | warn_objc_pointer_masking.Text |
| -Wdeprecated-writable-strings | dummy warning to enable -fconst-strings |
| -Wdirect-ivar-access | instance variable %0 is being directly accessed |
| -Wdistributed-object-modifiers | conflicting distributed object modifiers on return type in implementation of %0 |
| -Wdistributed-object-modifiers | conflicting distributed object modifiers on parameter type in implementation of %0 |
| -Wdivision-by-zero | division by zero is undefined |
| -Wdivision-by-zero | remainder by zero is undefined |
| -Wdocumentation | parameter '%0' not found in the function declaration |
| -Wdocumentation | not a Doxygen trailing comment |
| -Wduplicate-enum | element %0 has been implicitly assigned %1 which another element has been assigned |
| -Wduplicate-method-match | multiple declarations of method %0 found and ignored |
| -Wdynamic-class-memaccess | %select{destination for\|source of\|first operand of\|second operand of}0 this %1 call is a pointer to dynamic class %2 vtable pointer will be %select{overwritten\|copied\|moved\|compared}3 |
| -Wempty-body | switch statement has empty body |
| -Wempty-body | for loop has empty body |
| -Wempty-body | if statement has empty body |
| -Wempty-body | range-based for loop has empty body |
| -Wempty-body | while loop has empty body |
| -Wenum-compare | comparison of two values with different enumeration types%diff{ ($ and $)\|}0,1 |
| -Wenum-conversion | implicit conversion from enumeration type %0 to different enumeration type %1 |
| -Wexit-time-destructors | declaration requires an exit-time destructor |
| -Wexplicit-ownership-type | method parameter of type %0 with no explicit ownership |
| -Wextern-c-compat | %select{\|empty }0%select{struct\|union}1 has size 0 in C, %select{size 1\|non-zero size}2 in C++ |
| -Wextern-initializer | 'extern' variable has an initializer |
| -Wfloat-equal | comparing floating point with == or != is unsafe |
| -Wformat | "data argument position '%0' exceeds the number of data arguments (%1) |
| -Wformat | position arguments in format strings start counting at 1 (not 0) |
| -Wformat | invalid position specified for %select{field width\|field precision}0 |
| -Wformat | cannot mix positional and non-positional arguments in format string |
| -Wformat | values of type '%0' should not be used as format arguments add an explicit cast to %1 instead |
| -Wformat | format specifies type %0 but the argument has type %1 |
| -Wformat | zero field width in scanf format string is unused |
| -Wformat | no closing ']' for '%%[' in scanf format string |
| -Wformat | format string should not be a wide string |
| -Wformat | format string contains '\\0' within the string body |
| -Wformat | '%select{\*\|.\*}0' specified field %select{width\|precision}0 is missing a matching 'int' argument |
| -Wformat | field %select{width\|precision}0 should have type %1, but argument has type %2 |
| -Wformat | %select{field width\|precision}0 used with '%1' conversion specifier, resulting in undefined behavior |
| -Wformat | format string missing |
| -Wformat | incomplete format specifier |
| -Wformat | flag '%0' results in undefined behavior with '%1' conversion specifier |
| -Wformat | flag '%0' is ignored when flag '%1' is present |
| -Wformat | more '%%' conversions than data arguments |
| -Wformat | length modifier '%0' results in undefined behavior or no effect with '%1' conversion specifier |
| WCFString | |
| WCFString | |
| -Wformat-extra-args | data argument not used by format string |
| -Wformat-invalid-specifier | invalid conversion specifier '%0' |
| -Wformat-nonliteral | format string is not a string literal |
| -Wformat-security | format string is not a string literal (potentially insecure) |
| -Wformat-zero-length | format string is empty |
| -Wgcc-compat | GCC does not allow the 'cleanup' attribute argument to be anything other than a simple identifier |
| -Wglobal-constructors	declaration | requires a global constructor |
| -Wglobal-constructors	declaration | requires a global destructor |
| -Wgnu-conditional-omitted-operand | use of GNU ?: conditional expression extension, omitting middle operand |
| -Wheader-hygiene | using namespace directive in global context in header |
| -Widiomatic-parentheses | using the result of an assignment as a condition without parentheses |
| -Wignored-attributes | 'malloc' attribute only applies to functions returning a pointer type |
| -Wignored-attributes | %0 attribute only applies to %select{functions\|unions\|variables and functions\|functions and methods\|parameters\|functions, methods and blocks\|functions, methods, and classes\|functions, methods, and parameters\|classes\|variables\|methods\|variables, functions and labels\|fields and global variables\|structs\|variables, functions and tag types\|thread-local variables\|variables and fields\|variables, data members and tag types\|types and namespaces\|Objective-C interfaces}1 |
| -Wignored-attributes | '\%0' attribute cannot be specified on a definition |
| -Wignored-attributes | \_\_weak attribute cannot be specified on an automatic variable when ARC is not enabled |
| -Wignored-attributes | Objective-C GC does not allow weak variables on the stack |
| -Wignored-attributes | \_\_weak attribute cannot be specified on a field declaration |
| -Wignored-attributes | attribute %0 cannot be applied to %select{functions\|Objective-C method}1 without return value |
| -Wignored-attributes | attribute declaration must precede definition |
| -Wignored-attributes | attribute %0 is ignored, place it after \"%select{class\|struct\|union\|interface\|enum}1\" to apply attribute to type declaration |
| -Wignored-attributes | \_\_declspec attribute %0 is not supported |
| -Wignored-attributes | attribute %0 ignored, because it cannot be applied to a type |
| -Wignored-attributes | attribute %0 after definition is ignored |
| -Wignored-attributes | %0 attribute ignored |
| -Wignored-attributes | 'sentinel' attribute only supported for variadic %select{functions\|blocks}0 |
| -Wignored-attributes | 'sentinel' attribute requires named arguments |
| -Wignored-attributes | '%0' only applies to %select{function\|pointer\|Objective-C object or block pointer}1 types type here is %2 |
| -Wignored-attributes | 'nonnull' attribute applied to function with no pointer arguments |
| -Wignored-attributes | %0 attribute can only be applied to instance variables or properties |
| -Wignored-attributes | ibaction attribute can only be applied to Objective-C instance methods |
| -Wignored-attributes | %0 calling convention ignored on variadic function |
| -Wignored-attributes | %0 only applies to variables with static storage duration and functions |
| -Wignored-attributes | %0 attribute argument not supported: %1 |
| -Wignored-attributes | #pramga ms_struct can not be used with dynamic classes or structures |
| -Wignored-attributes | transparent union definition must contain at least one field transparent_union attribute ignored |
| -Wignored-attributes | first field of a transparent union cannot have %select{floating point\|vector}0 type %1 transparent_union attribute ignored |
| -Wignored-attributes | 'gnu_inline' attribute requires function to be marked 'inline', attribute ignored |
| -Wignored-attributes | calling convention %0 ignored for this target |
| -Wignored-attributes | transparent_union attribute can only be applied to a union definition attribute ignored |
| -Wignored-attributes | %select{alignment\|size}0 of field %1 (%2 bits) does not match the %select{alignment\|size}0 of the first field in transparent union transparent_union attribute ignored |
| -Wignored-attributes | attribute %0 is already applied |
| -Wignored-attributes | %0 attribute ignored for field of type %1 |
| -Wignored-attributes | %0 attribute ignored when parsing type |
| -Wignored-attributes | %0 attribute only applies to %select{functions\|methods\|properties}1 that return %select{an Objective-C object\|a pointer\|a non-retainable pointer}2 |
| -Wignored-attributes | %0 attribute only applies to %select{Objective-C object\|pointer}1 parameters |
| -Wignored-attributes | attribute %0 is already applied with different parameters |
| -Wignored-attributes | unknown visibility %0 |
| -Wignored-qualifiers | '%0' type qualifier%s1 on return type %plural{1:has\|:have}1 no effect |
| -Wignored-qualifiers | ARC %select{unused\|\_\_unsafe_unretained\|\_\_strong\|\_\_weak\|\_\_autoreleasing}0 lifetime qualifier on return type is ignored |
| -Wimplicit-atomic-properties | property is assumed atomic by default |
| -Wimplicit-atomic-properties | property is assumed atomic when auto-synthesizing the property |
| -Wimplicit-fallthrough | fallthrough annotation in unreachable code |
| -Wimplicit-fallthrough | unannotated fall-through between switch labels |
| -Wimplicit-fallthrough | fallthrough annotation does not directly precede switch label |
| -Wimplicit-function-declaration | implicit declaration of function %0 |
| -Wimplicit-function-declaration | use of unknown builtin %0 |
| -Wimplicit-retain-self | block implicitly retains 'self' explicitly mention 'self' to indicate this is intended behavior |
| -Wincompatible-library-redeclaration | incompatible redeclaration of library function %0 |
| -Wincomplete-implementation | method definition for %0 not found |
| -Winherited-variadic-ctor | inheriting constructor does not inherit ellipsis |
| -Winitializer-overrides | subobject initialization overrides initialization of other fields within its enclosing subobject |
| -Winitializer-overrides | initializer overrides prior initialization of this subobject |
| -Wint-to-pointer-cast | cast to %1 from smaller integer type %0 |
| -Wint-to-void-pointer-cast | cast to %1 from smaller integer type %0 |
| -Winvalid-iboutlet | IBOutletCollection properties should be copy\|strong and not assign |
| -Winvalid-iboutlet | %select{instance variable\|property}2 with %0 attribute must be an object type (invalid %1) |
| -Winvalid-noreturn | function %0 declared 'noreturn' should not return |
| -Winvalid-noreturn | function declared 'noreturn' should not return |
| -Wlarge-by-value-copy | return value of %0 is a large (%1 bytes) pass-by-value object pass it by reference instead ? |
| -Wlarge-by-value-copy | %0 is a large (%1 bytes) pass-by-value argument pass it by reference instead ? |
| -Wliteral-conversion | implicit conversion from %0 to %1 changes value from %2 to %3 |
| -Wliteral-range | magnitude of floating-point constant too large for type %0 maximum is %1 |
| -Wliteral-range | magnitude of floating-point constant too small for type %0 minimum is %1 |
| -Wlogical-not-parentheses | logical not is only applied to the left hand side of this comparison |
| -Wlogical-op-parentheses | '&&' within '||' |
| -Wloop-analysis | variable%select{s\| %1\|s %1 and %2\|s %1, %2, and %3\|s %1, %2, %3, and %4}0 used in loop condition not modified in loop body |
| -Wloop-analysis | variable %0 is %select{decremented\|incremented}1 both in the loop header and in the loop body |
| -Wmethod-signatures | conflicting parameter types in implementation of %0: %1 vs %2 |
| -Wmethod-signatures | conflicting return type in implementation of %0: %1 vs %2 |
| -Wmicrosoft | extra qualification on member %0 |
| -Wmismatched-method-attributes | attributes on method implementation and its declaration must match |
| -Wmismatched-parameter-types | conflicting parameter types in implementation of %0%diff{: $ vs $\|}1,2 |
| -Wmismatched-return-types | conflicting return type in implementation of %0%diff{: $ vs $\|}1,2 |
| -Wmissing-braces | suggest braces around initialization of subobject |
| -Wmissing-declarations | '%0' ignored on this declaration |
| -Wmissing-field-initializers | missing field '%0' initializer |
| -Wmissing-method-return-type | method has no return type specified defaults to 'id' |
| -Wmissing-noreturn | %select{function\|method}0 %1 could be declared with attribute 'noreturn' |
| -Wmissing-noreturn | block could be declared with attribute 'noreturn' |
| -Wmissing-prototypes | no previous prototype for function %0 |
| -Wmissing-variable-declarations | no previous extern declaration for non-static variable %0 |
| -Wmultiple-move-vbase | defaulted move assignment operator of %0 will move assign virtual base class %1 multiple times |
| -Wnested-anon-types | anonymous types declared in an anonymous union/struct are an extension |
| -Wno-typedef-redefinition | Redefinition of typedef '%0' is a C11 feature |
| -Wnon-literal-null-conversion | expression which evaluates to zero treated as a null pointer constant of " "type %0 |
| -Wnon-pod-varargs | second argument to 'va_arg' is of ARC ownership-qualified type %0 |
| -Wnon-pod-varargs | cannot pass %select{non-POD\|non-trivial}0 object of type %1 to variadic %select{function\|block\|method\|constructor}2 expected type from format string was %3 |
| -Wnon-pod-varargs | second argument to 'va_arg' is of non-POD type %0 |
| -Wnon-pod-varargs | cannot pass object of %select{non-POD\|non-trivial}0 type %1 through variadic %select{function\|block\|method\|constructor}2 call will abort at runtime |
| -Wnon-virtual-dtor | %0 has virtual functions but non-virtual destructor |
| -Wnonnull | null passed to a callee which requires a non-null argument |
| -Wnull-arithmetic | use of NULL in arithmetic operation |
| -Wnull-arithmetic | comparison between NULL and non-pointer %select{(%1 and NULL)\|(NULL and %1)}0 |
| -Wnull-dereference | indirection of non-volatile null pointer will be deleted, not trap |
| -Wobjc-autosynthesis-property-ivar-name-match | autosynthesized property %0 will use %select{\|synthesized}1 instance variable %2, not existing instance variable %3 |
| -Wobjc-forward-class-redefinition | redefinition of forward class %0 of a typedef name of an object type is ignored |
| -Wobjc-interface-ivars | declaration of instance variables in the interface is deprecated |
| -Wobjc-literal-compare | direct comparison of %select{an array literal\|a dictionary literal\|a numeric literal\|a boxed expression\|}0 has undefined behavior |
| -Wobjc-literal-missing-atsign | string literal must be prefixed by '@' |
| -Wobjc-method-access | instance method %objcinstance0 not found (return type defaults to 'id') did you mean %objcinstance2? |
| -Wobjc-method-access | class method %objcclass0 not found (return type defaults to 'id') did you mean %objcclass2? |
| -Wobjc-method-access | instance method %objcinstance0 not found (return type defaults to 'id') |
| -Wobjc-method-access | instance method %0 is being used on 'Class' which is not in the root class |
| -Wobjc-method-access | class method %objcclass0 not found (return type defaults to 'id') |
| -Wobjc-method-access | instance method %0 found instead of class method %1 |
| -Wobjc-missing-property-synthesis | auto property synthesis is synthesizing property not explicitly synthesized |
| -Wobjc-missing-super-calls | method possibly missing a [super %0] call |
| -Wobjc-noncopy-retain-block-property | retain'ed block property does not copy the block " "- use copy attribute instead |
| -Wobjc-nonunified-exceptions | can not catch an exception thrown with @throw in C++ in the non-unified exception model |
| -Wobjc-property-implementation | property %0 requires method %1 to be defined - use @dynamic or provide a method implementation in this category |
| -Wobjc-property-implementation | property %0 requires method %1 to be defined - use @synthesize, @dynamic or provide a method implementation in this class implementation |
| -Wobjc-property-implicit-mismatch | primary property declaration is implicitly strong while redeclaration in class extension is weak |
| -Wobjc-property-matches-cocoa-ownership-rule | property's synthesized getter follows Cocoa naming convention for returning 'owned' objects |
| -Wobjc-property-no-attribute | no 'assign', 'retain', or 'copy' attribute is specified - 'assign' is assumed |
| -Wobjc-property-no-attribute | default property attribute 'assign' not appropriate for non-GC object |
| -Wobjc-property-synthesis | auto property synthesis will not synthesize property '%0' because it is 'readwrite' but it will be synthesized 'readonly' via another property |
| -Wobjc-property-synthesis | auto property synthesis will not synthesize property '%0' because it cannot share an ivar with another synthesized property |
| -Wobjc-protocol-method-implementation | category is implementing a method which will also be implemented by its primary class |
| -Wobjc-protocol-property-synthesis | auto property synthesis will not synthesize property declared in a protocol |
| -Wobjc-redundant-literal-use | using %0 with a literal is redundant |
| -Wobjc-root-class | class %0 defined without specifying a base class |
| -Wobjc-string-compare	direct | comparison of a string literal has undefined behavior |
| -Wobjc-string-concatenation | concatenated NSString literal for an NSArray expression - possibly missing a comma |
| -Wover-aligned | type %0 requires %1 bytes of alignment and the default allocator only guarantees %2 bytes |
| -Woverloaded-shift-op-parentheses | overloaded operator %select{\|}0 has lower precedence than comparison operator |
| -Woverloaded-virtual | %q0 hides overloaded virtual %select{function\|functions}1 |
| -Woverriding-method-mismatch | conflicting distributed object modifiers on parameter type in declaration of %0 |
| -Woverriding-method-mismatch | conflicting parameter types in declaration of %0: %1 vs %2 |
| -Woverriding-method-mismatch | conflicting variadic declaration of method and its implementation |
| -Woverriding-method-mismatch | conflicting distributed object modifiers on return type in declaration of %0 |
| -Woverriding-method-mismatch | conflicting parameter types in declaration of %0%diff{: $ vs $\|}1,2 |
| -Woverriding-method-mismatch | conflicting return type in declaration of %0%diff{: $ vs $\|}1,2 |
| -Woverriding-method-mismatch | conflicting return type in declaration of %0: %1 vs %2 |
| -Wpacked | packed attribute is unnecessary for %0 |
| -Wpadded | padding %select{struct\|interface\|class}0 %1 with %2 %select{byte\|bit}3%select{\|s}4 to align anonymous bit-field |
| -Wpadded | padding %select{struct\|interface\|class}0 %1 with %2 %select{byte\|bit}3%select{\|s}4 to align %5 |
| -Wpadded | padding size of %0 with %1 %select{byte\|bit}2%select{\|s}3 to alignment boundary |
| -Wparentheses | using the result of an assignment as a condition without parentheses |
| -Wparentheses | %0 has lower precedence than %1 %1 will be evaluated first |
| -Wparentheses | operator '?:' has lower precedence than '%0' '%0' will be evaluated first |
| -Wparentheses-equality | equality comparison with extraneous parentheses |
| -Wpointer-arith | subtraction of pointers to type %0 of zero size has undefined behavior |
| -Wpredefined-identifier-outside-function | predefined identifier is only valid inside function |
| -Wprivate-extern | use of \_\_private_extern\_\_ on a declaration may not produce external symbol private to the linkage unit and is deprecated |
| -Wprotocol | method %0 in protocol not implemented |
| -Wprotocol-property-synthesis-ambiguity | property of type %0 was selected for synthesis |
| -Wreadonly-iboutlet-property | readonly IBOutlet property '%0' when auto-synthesized may not work correctly with 'nib' loader |
| -Wreadonly-setter-attrs | property attributes '%0' and '%1' are mutually exclusive |
| -Wreceiver-expr | receiver type %0 is not 'id' or interface pointer, consider casting it to 'id' |
| -Wreceiver-forward-class | receiver type %0 for instance message is a forward declaration |
| -Wreceiver-is-weak | weak %select{receiver\|property\|implicit property}0 may be unpredictably set to nil |
| -Wreinterpret-base-class | 'reinterpret_cast' %select{from\|to}3 class %0 %select{to\|from}3 its %select{virtual base\|base at non-zero offset}2 %1 behaves differently from 'static_cast' |
| -Wreorder | %select{field\|base class}0 %1 will be initialized after %select{field\|base}2 %3 |
| -Wrequires-super-attribute | %0 attribute cannot be applied to %select{methods in protocols\|dealloc}1 |
| -Wreturn-stack-address | returning address of local temporary object |
| -Wreturn-stack-address | returning address of label, which is local |
| -Wreturn-stack-address | address of stack memory associated with local variable %0 returned |
| -Wreturn-stack-address | reference to stack memory associated with local variable %0 returned |
| -Wreturn-stack-address | returning reference to local temporary object |
| -Wreturn-type | control may reach end of non-void function |
| -Wreturn-type | non-void %select{function\|method}1 %0 should return a value, DefaultError |
| -Wreturn-type | control reaches end of non-void function |
| -Wreturn-type-c-linkage | %0 has C-linkage specified, but returns incomplete type %1 which could be incompatible with C |
| -Wreturn-type-c-linkage | %0 has C-linkage specified, but returns user-defined type %1 which is incompatible with C |
| -Wsection | section does not match previous declaration |
| -Wselector | creating selector for nonexistent method %0 |
| -Wselector-type-mismatch | multiple selectors named %0 found |
| -Wself-assign | explicitly assigning a variable of type %0 to itself |
| -Wself-assign-field | assigning %select{field\|instance variable}0 to itself |
| -Wsentinel | missing sentinel in %select{function call\|method dispatch\|block call}0 |
| -Wsentinel | not enough variable arguments in %0 declaration to fit a sentinel |
| -Wshadow | declaration shadows a %select{" "local variable\|" "variable in %2\|" "static data member of %2\|" "field of %2}1 |
| -Wshadow-ivar | local declaration of %0 hides instance variable |
| -Wshift-count-negative | shift count is negative |
| -Wshift-count-overflow | shift count = width of type |
| -Wshift-op-parentheses | operator '%0' has lower precedence than '%1' '%1' will be evaluated first |
| -Wshift-overflow | signed shift result (%0) requires %1 bits to represent, but %2 only has %3 bits |
| -Wshift-sign-overflow	signed | shift result (%0) sets the sign bit of the shift expression's type (%1) and becomes negative |
| -Wshorten-64-to-32 | implicit conversion loses integer precision: %0 to %1 |
| -Wsign-compare | comparison of integers of different signs: %0 and %1 |
| -Wsign-conversion | implicit conversion changes signedness: %0 to %1 |
| -Wsign-conversion | operand of ? changes signedness: %0 to %1 |
| -Wsizeof-array-argument | sizeof on array function parameter will return size of %0 instead of %1 |
| -Wsizeof-array-decay | sizeof on pointer operation will return size of %0 instead of %1 |
| -Wsizeof-pointer-memaccess | '%0' call operates on objects of type %1 while the size is based on a " "different type %2 |
| -Wsizeof-pointer-memaccess | argument to 'sizeof' in %0 call is the same pointer type %1 as the %select{destination\|source}2 expected %3 or an explicit length |
| -Wsometimes-uninitialized | variable %0 is %select{used\|captured}1 uninitialized whenever %select{'%3' condition is %select{true\|false}4\|'%3' loop %select{is entered\|exits because its condition is false}4\|'%3' loop %select{condition is true\|exits because its condition is false}4\|switch %3 is taken\|its declaration is reached\|%3 is called}2 |
| -Wstatic-local-in-inline | non-constant static local variable in inline function may be different in different files |
| -Wstatic-self-init | static variable %0 is suspiciously used within its own initialization |
| -Wstrict-selector-match | multiple methods named %0 found |
| -Wstring-compare | result of comparison against %select{a string literal\|@encode}0 is unspecified (use strncmp instead) |
| -Wstring-conversion | implicit conversion turns string literal into bool: %0 to %1 |
| -Wstring-plus-char | adding %0 to a string pointer does not append to the string |
| -Wstring-plus-int | adding %0 to a string does not append to the string |
| -Wstrlcpy-strlcat-size | size argument in %0 call appears to be size of the source expected the size of the destination |
| -Wstrncat-size | the value of the size argument in 'strncat' is too large, might lead to a " "buffer overflow |
| -Wstrncat-size | size argument in 'strncat' call appears " "to be size of the source |
| -Wstrncat-size | the value of the size argument to 'strncat' is wrong |
| -Wsuper-class-method-mismatch | method parameter type %diff{$ does not match super class method parameter type $\|does not match super class method parameter type}0,1 |
| -Wswitch | overflow converting case value to switch condition type (%0 to %1) |
| -Wswitch | case value not in enumerated type %0 |
| -Wswitch | %0 enumeration values not handled in switch: %1, %2, %3... |
| -Wswitch | enumeration values %0 and %1 not handled in switch |
| -Wswitch | enumeration value %0 not handled in switch |
| -Wswitch | enumeration values %0, %1, and %2 not handled in switch |
| -Wswitch-enum | enumeration values %0, %1, and %2 not explicitly handled in switch |
| -Wswitch-enum | enumeration values %0 and %1 not explicitly handled in switch |
| -Wswitch-enum | %0 enumeration values not explicitly handled in switch: %1, %2, %3... |
| -Wswitch-enum | enumeration value %0 not explicitly handled in switch |
| -Wtautological-compare | comparison of %0 unsigned%select{\| enum}2 expression is always %1 |
| -Wtautological-compare | %select{self-\|array }0comparison always evaluates to %select{false\|true\|a constant}1 |
| -Wtautological-compare | comparison of unsigned%select{\| enum}2 expression %0 is always %1 |
| -Wtautological-constant-out-of-range-compare | comparison of constant %0 with expression of type %1 is always %select{false\|true}2 |
| -Wthread-safety-analysis | locking '%0' that is already locked |
| -Wthread-safety-analysis | cannot call function '%0' while mutex '%1' is locked |
| -Wthread-safety-analysis | %select{reading\|writing}2 the value pointed to by '%0' requires locking %select{'%1'\|'%1' exclusively}2 |
| -Wthread-safety-analysis | unlocking '%0' that was not locked |
| -Wthread-safety-analysis | mutex '%0' is locked exclusively and shared in the same scope |
| -Wthread-safety-analysis | calling function '%0' requires %select{shared\|exclusive}2 lock on '%1' |
| -Wthread-safety-analysis | %select{reading\|writing}2 variable '%0' requires locking %select{'%1'\|'%1' exclusively}2 |
| -Wthread-safety-analysis | cannot resolve lock expression |
| -Wthread-safety-analysis | expecting mutex '%0' to be locked at the end of function |
| -Wthread-safety-analysis | mutex '%0' is not locked on every path through here |
| -Wthread-safety-analysis | %select{reading\|writing}1 the value pointed to by '%0' requires locking %select{any mutex\|any mutex exclusively}1 |
| -Wthread-safety-analysis | %select{reading\|writing}1 variable '%0' requires locking %select{any mutex\|any mutex exclusively}1 |
| -Wthread-safety-analysis | mutex '%0' is still locked at the end of function |
| -Wthread-safety-analysis | expecting mutex '%0' to be locked at start of each loop |
| -Wthread-safety-attributes | ignoring %0 attribute because its argument is invalid |
| -Wthread-safety-attributes | %0 attribute only applies to %select{fields and global variables\|functions and methods\|classes and structs}1 |
| -Wthread-safety-attributes | %0 attribute requires arguments that are class type or point to class type type here is '%1' |
| -Wthread-safety-attributes | %0 attribute can only be applied in a context annotated with 'lockable' attribute |
| -Wthread-safety-attributes | %0 attribute requires arguments whose type is annotated with 'lockable' attribute type here is '%1' |
| -Wthread-safety-attributes | '%0' only applies to pointer types type here is %1 |
| -Wthread-safety-beta | Thread safety beta warning. |
| -Wthread-safety-precise | %select{reading\|writing}2 the value pointed to by '%0' requires locking %select{'%1'\|'%1' exclusively}2 |
| -Wthread-safety-precise | %select{reading\|writing}2 variable '%0' requires locking %select{'%1'\|'%1' exclusively}2 |
| -Wthread-safety-precise | calling function '%0' requires %select{shared\|exclusive}2 lock on '%1' |
| -Wtype-safety | this type tag was not designed to be used with this function |
| -Wtype-safety | specified %0 type tag requires a null pointer |
| -Wtype-safety | argument type %0 doesn't match specified '%1' type tag %select{that requires %3\|}2 |
| -Wundeclared-selector | undeclared selector %0 did you mean %1? |
| -Wundeclared-selector | undeclared selector %0 |
| -Wundefined-inline | inline function %q0 is not defined |
| -Wundefined-internal | %select{function\|variable}0 %q1 has internal linkage but is not defined |
| -Wundefined-reinterpret-cast | dereference of type %1 that was reinterpret_cast from type %0 has undefined behavior |
| -Wundefined-reinterpret-cast | reinterpret_cast from %0 to %1 has undefined behavior |
| -Wuninitialized | reference %0 is not yet bound to a value when used within its own initialization |
| -Wuninitialized | field %0 is uninitialized when used here |
| -Wuninitialized | block pointer variable %0 is uninitialized when captured by block |
| -Wuninitialized | variable %0 is uninitialized when used within its own initialization |
| -Wuninitialized | variable %0 is uninitialized when %select{used here\|captured by block}1 |
| -Wuninitialized | reference %0 is not yet bound to a value when used here |
| -Wunneeded-internal-declaration | %select{function\|variable}0 %1 is not needed and will not be emitted |
| -Wunneeded-internal-declaration | 'static' function %0 declared in header file should be declared 'static inline' |
| -Wunneeded-member-function | member function %0 is not needed and will not be emitted |
| -Wunreachable-code | will never be executed |
| -Wunsequenced | multiple unsequenced modifications to %0 |
| -Wunsequenced | unsequenced modification and access to %0 |
| -Wunsupported-friend | dependent nested name specifier '%0' for friend template declaration is not supported ignoring this friend declaration |
| -Wunsupported-friend | dependent nested name specifier '%0' for friend class declaration is not supported turning off access control for %1 |
| -Wunsupported-visibility | target does not support 'protected' visibility using 'default' |
| -Wunused-comparison | %select{equality\|inequality}0 comparison result unused |
| -Wunused-const-variable | unused variable %0 |
| -Wunused-exception-parameter | unused exception parameter %0 |
| -Wunused-function | unused function %0 |
| -Wunused-label | unused label %0 |
| -Wunused-member-function | unused member function %0 |
| -Wunused-parameter | unused parameter %0 |
| -Wunused-private-field | private field %0 is not used |
| -Wunused-property-ivar | ivar %0 which backs the property is not referenced in this property's accessor |
| -Wunused-result | ignoring return value of function declared with warn_unused_result attribute |
| -Wunused-value | ignoring return value of function declared with %0 attribute |
| -Wunused-value | expression result unused should this cast be to 'void'? |
| -Wunused-value | expression result unused |
| -Wunused-variable | unused variable %0 |
| -Wunused-volatile-lvalue | expression result unused assign into a variable to force a volatile load |
| -Wused-but-marked-unused | %0 was marked unused but was used |
| -Wuser-defined-literals | user-defined literal suffixes not starting with '_' are reserved%select{ no literal will invoke this operator\|}0 |
| -Wvarargs | second parameter of 'va_start' not last named argument |
| -Wvarargs | 'va_start' has undefined behavior with reference types |
| -Wvarargs | second argument to 'va_arg' is of promotable type %0 this va_arg has undefined behavior because arguments will be promoted to %1 |
| -Wvector-conversion | incompatible vector types %select{\%diff{assigning to \$ from \$\|assigning to different types}0,1\|%diff{passing \$ to parameter of type \$\|passing to parameter of different type}0,1\|%diff{returning \$ from a function with result type \$\|returning from function with different return type}0,1\|%diff{converting \$ to type \$\|converting between types}0,1\|%diff{initializing \$ with an expression of type \$\|initializing with expression of different type}0,1\|%diff{sending \$ to parameter of type \$\|sending to parameter of different type}0,1\|%diff{casting \$ to type \$\|casting between types}0,1}2 |
| -Wvexing-parse | parentheses were disambiguated as a function declaration |
| -Wvexing-parse | empty parentheses interpreted as a function declaration |
| -Wvisibility | declaration of %0 will not be visible outside of this function |
| -Wvisibility | redefinition of %0 will not be visible outside of this function |
| -Wvla | variable length array used |
| -Wvla-extension | variable length arrays are a C99 feature |
| -Wweak-template-vtables | explicit template instantiation %0 will emit a vtable in every translation unit |
| -Wweak-vtables | %0 has no out-of-line virtual method definitions; its vtable will be emitted in every translation unit |


#### **Lexer Warnings**

| Warning | Message |
|:---------- |:---------- |
| -W#pragma-messages | %0 |
| -W#warnings | %0 |
| -W#warnings | %0 |
| -Wambiguous-macro | ambiguous expansion of macro %0 |
| -Wauto-import | treating \#\%select{include\|import\|include_next\|\_\_include_macros}0 as an import of module '%1' |
| -Wbackslash-newline-escape | backslash and newline separated by space |
| -Wc++11-compat | identifier after literal will be treated as a user-defined literal suffix in C++11 |
| -Wc++11-compat | '%0' is a keyword in C++11 |
| -Wc++98-c++11-compat | digit separators are incompatible with C++ standards before C++1y |
| -Wc++98-c++11-compat-pedantic | binary integer literals are incompatible with C++ standards before C++1y |
| -Wc++98-compat | raw string literals are incompatible with C++98 |
| -Wc++98-compat | unicode literals are incompatible with C++98 |
| -Wc++98-compat | universal character name referring to a control character is incompatible with C++98 |
| -Wc++98-compat | '::' is treated as digraph ':' (aka '[') followed by ':' in C++98 |
| -Wc++98-compat | using this character in an identifier is incompatible with C++98 |
| -Wc++98-compat | specifying character '%0' with a universal character name is incompatible with C++98 |
| -Wc++98-compat-pedantic | variadic macros are incompatible with C++98 |
| -Wc++98-compat-pedantic | #line number greater than 32767 is incompatible with C++98 |
| -Wc++98-compat-pedantic | C++98 requires newline at end of file |
| -Wc++98-compat-pedantic | empty macro arguments are incompatible with C++98 |
| -Wc99-compat | unicode literals are incompatible with C99 |
| -Wc99-compat | %select{using this character in an identifier\|starting an identifier with this character}0 is incompatible with C99 |
| -Wcomment | '/\*' within block comment |
| -Wcomment | escaped newline between '\*/' characters at block comment end |
| -Wdisabled-macro-expansion | disabled expansion of recursive macro |
| -Wheader-guard | %0 is used as a header guard here, followed by #define of a different macro |
| -Wignored-attributes | unknown attribute '%0' |
| -Wincomplete-module | header '%0' is included in module '%1' but not listed in module map |
| -Wincomplete-umbrella | umbrella header for module '%0' does not include header '%1' |
| -Winvalid-token-paste	pasting | formed '%0', an invalid preprocessing token, DefaultError |
| -Wmalformed-warning-check | __has_warning expected option name (e.g. \"-Wundef\") |
| -Wnewline-eof | no newline at end of file |
| -Wnull-character | null character ignored |
| -Wnull-character | null character(s) preserved in string literal |
| -Wnull-character | null character(s) preserved in character literal |
| -Wtrigraphs | ignored trigraph would end block comment |
| -Wtrigraphs | trigraph ignored |
| -Wundef | %0 is not defined, evaluates to 0 |
| -Wunicode | universal character names are only valid in C99 or C++ treating as '\\' followed by identifier |
| -Wunicode | \\%0 used with no following hex digits treating as '\\' followed by identifier |
| -Wunicode | incomplete universal character name treating as '\\' followed by identifier |
| -Wunicode | universal character name refers to a surrogate character |
| -Wunknown-pragmas | unknown pragma ignored |
| -Wunknown-pragmas | pragma STDC FENV_ACCESS ON is not supported, ignoring pragma |
| -Wunused-macros | macro is not used |

#### **Parser Warnings**

| Warning | Message |
|:---------- |:---------- |
| -Warc-bridge-casts-disallowed-in-nonarc | '%0' casts have no effect when not using ARC |
| -Wattributes | unknown \_\_declspec attribute %0 ignored |
| -Wavailability | 'unavailable' availability overrides all other availability information |
| -Wc++11-compat | use of right-shift operator ('') in template argument will require parentheses in C++11 |
| -Wc++11-compat | 'auto' storage class specifier is redundant and incompatible with C++11 |
| -Wc++98-c++11-compat | 'decltype(auto)' type specifier is incompatible with C++ standards before C++1y |
| -Wc++98-compat | range-based for loop is incompatible with C++98 |
| -Wc++98-compat | alias declarations are incompatible with C++98 |
| -Wc++98-compat | in-class initialization of non-static data members is incompatible with C++98 |
| -Wc++98-compat | defaulted function definitions are incompatible with C++98 |
| -Wc++98-compat | rvalue references are incompatible with C++98 |
| -Wc++98-compat | reference qualifiers on functions are incompatible with C++98 |
| -Wc++98-compat | inline namespaces are incompatible with C++98 |
| -Wc++98-compat | generalized initializer lists are incompatible with C++98 |
| -Wc++98-compat | trailing return types are incompatible with C++98 |
| -Wc++98-compat | enumeration types with a fixed underlying type are incompatible with C++98 |
| -Wc++98-compat | alignof expressions are incompatible with C++98 |
| -Wc++98-compat | '%0' keyword is incompatible with C++98 |
| -Wc++98-compat | 'decltype' type specifier is incompatible with C++98 |
| -Wc++98-compat | deleted function definitions are incompatible with C++98 |
| -Wc++98-compat | consecutive right angle brackets are incompatible with C++98 (use '> >') |
| -Wc++98-compat | static_assert declarations are incompatible with C++98 |
| -Wc++98-compat | scoped enumerations are incompatible with C++98 |
| -Wc++98-compat | lambda expressions are incompatible with C++98 |
| -Wc++98-compat | attributes are incompatible with C++98 |
| -Wc++98-compat | 'alignas' is incompatible with C++98 |
| -Wc++98-compat | noexcept specifications are incompatible with C++98 |
| -Wc++98-compat | literal operators are incompatible with C++98 |
| -Wc++98-compat | noexcept expressions are incompatible with C++98 |
| -Wc++98-compat | 'nullptr' is incompatible with C++98 |
| -Wc++98-compat-pedantic | extra '' outside of a function is incompatible with C++98 |
| -Wc++98-compat-pedantic | extern templates are incompatible with C++98 |
| -Wc++98-compat-pedantic | commas at the end of enumerator lists are incompatible with C++98 |
| -Wdangling-else | add explicit braces to avoid dangling else |
| -Wdeprecated | Use of 'long' with '\_\_vector' is deprecated |
| -Wdeprecated-declarations | use of C-style parameters in Objective-C method declarations is deprecated |
| -Wdeprecated-register | 'register' storage class specifier is deprecated |
| -Wduplicate-decl-specifier | duplicate '%0' declaration specifier |
| -Wextra-semi | extra ';' after member function definition |
| -Wextra-tokens | "extra tokens at the end of '#pragma omp %0' are ignored |
| -Wgcc-compat | GCC does not allow %0 attribute in this position on a function definition |
| -Wignored-attributes | attribute %0 ignored, because it is not attached to a declaration |
| -Wmicrosoft-exists | dependent %select{\_\_if\_not\_exists\|\_\_if\_exists}0 declarations are ignored |
| -Wmissing-selector-name | %0 used as the name of the previous parameter rather than as part of the selector |
| -Wsemicolon-before-method-body | semicolon before method body is ignored |
| -Wsource-uses-openmp | "unexpected '#pragma omp ...' in program |
| -Wstatic-inline-explicit-instantiation | ignoring '%select{static\|inline}0' keyword on explicit template instantiation |

<!--markdown使用html标签嵌入折叠标签-->
<!--<details>
  <summary>点击时的区域标题</summary>
  <p>显示详细内容一</p>
  <p>显示详细内容二</p>
</details>-->
