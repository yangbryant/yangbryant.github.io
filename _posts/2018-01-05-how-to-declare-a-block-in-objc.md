---
layout: post
title: Objective-C声明Block的几种方式
date: 2018-01-05 15:37:00 +08:00
tags: 初学者
---

***

### 一.概述

#### 1. As a local variable:

```objective-c
returnType (^blockName)(parameterTypes) = ^returnType(parameters) {...};
```

#### 2. As a property:

```objective-c
@property (nonatomic, copy, nullability) returnType (^blockName)(parameterTypes);
```

#### 3. As a method parameter:

```objective-c
- (void)someMethodThatTakesABlock:(returnType (^nullability)(parameterTypes))blockName;
```

#### 4. As a argument to a method call:

```objective-c
[someObject someMethodThatTakesABlock:^returnType (parameters) {...}];
```

#### 5. As a typedef:

```objective-c
typedef returnType (^TypeName)(parameterTypes);
TypeName blockName = ^returnType(parameters) {...};
```

### 二.参考链接

此文参考于 [goshdarnblocksyntax][Link_1],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: http://goshdarnblocksyntax.com/