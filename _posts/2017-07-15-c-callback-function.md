---
layout: post
title: C语言实现 Callback Function 例子
date: 2017-07-15 15:00:00 +08:00
author: Srefan
catalog: true
tags:
    - C/C++
    - 进阶
    - 深阅读
---

***

### 一.概述

* C语言, `callback function`是比较值得一学的一个知识点, 也是比较常用,优化代码结构的一个方法.
* 主要概念就是: **传回某个函数的指针, 调用者即可通过该函数指针直接执行函数**.

### 二.实现

* 定义回调函数的原型

```c
// 定义callback function类型
typedef int (*exampleCallback)(char *);
```

* 声明一个该函数指针的变量.

```c
// 声明函数指针变量 examplecb, 指向 NULL
static exampleCallback examplecb = NULL;
```

* 实现回调函数的功能实现

```c
// 实现回调函数, 输出 Hello World
static
int showHelloworld(char * name)
{
    printf("Hello World, %s.\n", name);
    return 0;
}
```

* 实现 `bsp_show` 函数, 功能为: 完成倒计时后, 执行 **回调函数**

```c
static
void bsp_show(int time, char *name)
{
    while (time > 0)
    {
        printf("%d\n", time);

        time--;
        sleep(1);

        if ((time == 0) && examplecb)
        {
          	// 调用回调函数
            examplecb(name); 
          	// 清空回调函数
            examplecb = NULL;
        }
    }
}
```

* 实现 `main` 函数, 程序启动的入口

```c
// 由 main 执行, 程序入口
int main(int argc, char *argv[])
{
    if (argc < 3){
        printf("Usage: %s base exponent \n", argv[0]);
        return 1;
    }
    
    int timeout = atoi(argv[1]);
    char * name = argv[2];

    // 设置函数指针为 showHelloworld 回调函数
    examplecb = showHelloworld;

    bsp_show(timeout, name);
    return 0;
}
```

* 附 **完整代码** :

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

// 定义callback function类型
typedef int (*exampleCallback)(char *);

// 声明函数指针变量 examplecb, 指向 NULL
static exampleCallback examplecb = NULL;

// 实现回调函数, 输出 Hello World
static
int showHelloworld(char * name)
{
    printf("Hello World, %s.\n", name);
    return 0;
}

static
void bsp_show(int time, char *name)
{
    while (time > 0)
    {
        printf("%d\n", time);

        time--;
        sleep(1);

        if ((time == 0) && examplecb)
        {
            // 调用回调函数
            examplecb(name); 
            // 清空回调函数
            examplecb = NULL;
        }
    }
}

int main(int argc, char *argv[])
{
    if (argc < 3){
        printf("Usage: %s base exponent \n", argv[0]);
        return 1;
    }

    int timeout = atoi(argv[1]);
    char * name = argv[2];

    // 设置函数指针为 showHelloworld 回调函数
    examplecb = showHelloworld;

    bsp_show(timeout, name);
    return 0;
}
```

### 三.参考链接

此文参考于 [易春木博客][Link_1],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

本文的 `代码` 在此处, [点击下载][Link_2].


[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: http://eeepage.info/examplecallback-function/
[Link_2]: https://github.com/yangbryant/cmake_tutorial/tree/master/demo9/