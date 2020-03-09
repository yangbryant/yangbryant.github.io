---
layout: post
title: 操作系统基础(一) -- 数据单元
date: 2017-02-07 16:12:00 +08:00
author: Srefan
catalog: true
tags:
    - 入门
    - 操作系统
---

***

#### 操作系统基础:

[操作系统基础(一) -- 数据单元][arch]  
[操作系统基础(二) -- 中断和系统调用][interrupt_and_syscall]  
[操作系统基础(三) -- 并发技术][concurrency]  
[操作系统基础(四) -- 内存管理基础][memory_management]  
[操作系统基础(五) -- 磁盘与文件][disk_and_file]  

***

### 位(Bit), 字节(Byte), 字(Word)

* 位: 计算机 **最小数据单位** .状态只能是 **0或1** .
* 字节: **8个位构成一个字节** ,存储空间的 **基本计量单位** .一个字节存储 **1个字母** 或者 **半个汉字** .
* 字: 计算机进行 **数据处理和运算的单位**. 由若干个字节构成,字的位数叫字长,机器不同,字长不同. 8位机: 1个字 = 1字节 (字长 = 8). 16位机: 1个字 = 2字节 (字长 = 16).

***

### 字节序

* 占内存多于一个字节类型的数据在内存中的存放顺序.
1. **小端编码(Little-endian)** : 低字节数据存放在内存低地址处,高字节数据存放在内存高地址处. 使用平台: **PC平台**.
2. **大端编码(Big-endian)** : 高字节数据存放在低地址处,低字节数据存放在高地址处. 使用平台: **嵌入式平台**, **所有网络协议**.

```plain
数字 0x12345678 在两种不同字节序CPU中的存储顺序:

Big Endian
低地址                                            高地址
---------------------------------------------------->
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     12     |      34    |     56      |     78    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

Little Endian
低地址                                            高地址
---------------------------------------------------->
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     78     |      56    |     34      |     12    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

```

* 联合体union的存放顺序是 **成员都从低地址开始存放** , 利用该特性, 就能判断CPU对内存采用Little-endian还是Big-endian模式读写.

```c
union test{
    short i;
    char str[sizeof(short)];
}tt;

void main()
{
    tt.i = 0x0102;
    if(sizeof(short) == 2)
    {
        if(tt.str[0] == 1 && tt.str[1] == 2)
            printf("大端字节序");
        else if(tt.str[0] = 2 && tt.str[1] == 1)
            printf("小端字节序");
        else
            printf("结果未知");
    }
    else
        printf("sizof(short)=%d,不等于2",sizeof(short));
}

```

***

### 字节对齐

* 现代计算机中内存空间都是按照byte划分的,从理论上讲似乎对任何类型的变量的访问可以从任何地址开始,但实际情况是在访问特定类型变量时经常在特定的内存地址访问,这就需要各种类型数据按照一定的规则在空间上排列,而不是顺序的一个接一个的排放,这就是对齐.
* 为什么要进行字节对齐:
1. 某些平台只能在特定的地址处访问特定类型的数据.
2. 最根本的原因是效率问题,字节对齐能 **提⾼存取数据的速度**.
* 字节对齐的原则:
1. 数据成员对齐规则: **结构体(struct)** 或 **联合体(union)** 的数据成员,第一个数据成员放在 **offset为0** 的地方,以后每个数据成员存储的 **起始位置** 要从 **该成员大小或者成员的子成员大小** (只要该成员有子成员,比如说是数组,结构体等)的 **整数倍** 开始(比如int在32位机为4字节,则要从4的整数倍地址开始存储).
2. 结构体作为成员: 如果一个结构里有某些结构体成员,则结构体成员要从其内部 **最大元素大小的整数倍地址** 开始存储.(struct a里存有struct b, b里有char,int,double等元素, 那b应该从8的整数倍开始存储.)
3. 收尾工作: 结构体的总大小, 也就是sizeof的结果,必须是 **其内部最大成员的整数倍** ,不足的要补齐.

***

此文参考于 [hit-alibaba.github.io][hit-alibaba.github.io],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[hit-alibaba.github.io]: https://hit-alibaba.github.io/interview/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[arch]: /2017/02/basics-about-arch/ 'arch'
[interrupt_and_syscall]: /2017/02/basics-about-interrupt-and-syscall/ 'interrupt_and_syscall'
[concurrency]: /2017/02/basics-about-concurrency/ 'concurrency'
[memory_management]: /2017/02/basics-about-memory-management/ 'memory_management'
[disk_and_file]: /2017/02/basics-about-disk-and-file/ 'disk_and_file'