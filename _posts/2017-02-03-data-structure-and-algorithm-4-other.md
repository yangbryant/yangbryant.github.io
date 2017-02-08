---
layout: post
title: 数据结构与算法基础(四) -- 其他
date: 2017-02-03 09:05:00 +08:00
tags: 温故而知新
---

***

#### 数据结构与算法基础:

[数据结构与算法基础(一) -- 链表][linked_list]  
[数据结构与算法基础(二) -- 树][tree]  
[数据结构与算法基础(三) -- 排序算法][sort]  
[数据结构与算法基础(四) -- 其他][other]  

***

### 哈希表

* 哈希表, Hash Table, 散列表, 根据 **键值对** (**Key-Value**)直接访问的数据结构.
* 通过把键值对映射到表中一个位置来访问记录,加快查找速度.
* 哈希表的实现需要解决2个问题: **哈希函数** 和 **冲突解决**.
* [哈希表的C语言实现][hash_table_implement].

#### 哈希函数

* 哈希函数,也叫散列函数,对不同输出值得到一个 **固定长度** 的消息摘要.
* 理想的哈希函数对于不同的输入值应该产生不同的结构,同时散列结果应当具有**同一性**(输出值尽量均匀)和**雪崩效应**(微小的输入值变化使得输出值发生巨大的变化).

#### 冲突解决

* 当两个不同的输入值对应一个输出值时,就会产生'**碰撞**',这时候就需要冲突解决.
* 冲突解决的方法:**开放定址法**, **链地址法**, **建立公共溢出区**.使用最多的是 **链地址法**.
* 链地址法基本思路: 为每个 Hash 值建立一个单链表,当发生冲突时,将记录插入到链表中.

> 有 8 个元素 {a, b, c, d, e, f, g, h},采用某种哈希函数得到的地址分别为: {0, 2, 4, 1, 0, 7, 8, 2}, 当哈希表长度为 10 时,采用链地址法解决冲突的哈希表如下图所示:

![哈希表冲突解决实例][hash_table]

***

### 搜索

***

### 字符串

***

### 向量/矩阵

***

### 随机

#### 洗牌算法

* 利用一次循环等概率的取到不同的元素.
* 逻辑流程: 如果元素存在于数组中,即可将每次 Random 到的元素 与 最后一个元素 进行交换,然后 count-- .

```c++
#include <iostream>
#include <ctime> 
using namespace std;

const int maxn = 10;

int a[maxn];

int randomInt(int a) {
    return rand()%a;
}
void swapTwoElement(int*x,int*y) {
     int temp;
     temp=*x;
     *x=*y;
     *y=temp;
}

int main(){
    int count = sizeof(a)/sizeof(int);
    int count_b = count;
    srand((unsigned)time(NULL));
    for (int i = 0; i < count; ++i) { a[i] = i; }
    for (int i = 0; i < count_b; ++i) {
        int random = randomInt(count);
        cout<<a[random]<<" ";
        swapTwoElement(&a[random],&a[count-1]);
        count--;
    }
}
```

***

### 贪心

* 在对问题求解时,总是做出在 **当前看来是最好的选择** ,不从 **整体最优** 上考虑,仅仅是 **局部最优解**.
* 基本思路:
1. 建立数学模型来描述问题.
2. 把求解的问题分成若干个子问题.
3. 对每一子问题求解,得到子问题的局部最优解.
4. 把子问题的解局部最优解合成原来解问题的一个解.
* 贪心策略适用的前提是: 局部最优策略能导致产生全局最优解.

***

### 动态规划

***

此文参考于 [hit-alibaba.github.io][hit-alibaba.github.io],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[hash_table_implement]: http://www.cnblogs.com/xiekeli/archive/2012/01/13/2321207.html
[hit-alibaba.github.io]: https://hit-alibaba.github.io/interview/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[hash_table]: /assets/images/data_structure/hash_table.jpg 'hash_table'

[linked_list]: /2017/02/data-structure-and-algorithm-1-linked-list/
[tree]: /2017/02/data-structure-and-algorithm-2-tree/
[sort]: /2017/02/data-structure-and-algorithm-3-sort/
[other]: /2017/02/data-structure-and-algorithm-4-other/
