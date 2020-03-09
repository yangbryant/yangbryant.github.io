---
layout: post
title: 排序算法的详细介绍(八) -- 鸡尾酒排序
date: 2017-02-04 11:35:00 +08:00
author: Srefan
catalog: true
tags:
    - 入门
    - 数据结构
---

***

#### 常见排序算法归档介绍:

[排序算法的详细介绍(一) -- 插入排序][insertion_sort]  
[排序算法的详细介绍(二) -- 冒泡排序][bubble_sort]  
[排序算法的详细介绍(三) -- 快速排序][quick_sort]  
[排序算法的详细介绍(四) -- 选择排序][selection_sort]  
[排序算法的详细介绍(五) -- 堆排序][heap_sort]  
[排序算法的详细介绍(六) -- 希尔排序][shell_sort]  
[排序算法的详细介绍(七) -- 归并排序][merge_sort]  
[排序算法的详细介绍(八) -- 鸡尾酒排序][cocktail_sort]  
[排序算法的详细介绍(九) -- 猴子排序][bogo_sort]  
[排序算法的详细介绍(十) -- 桶排序][bucket_sort]  
[排序算法的详细介绍(十一) -- 基数排序][radix_sort]  

***

### 鸡尾酒排序 Cocktail Sort/Shaker Sort

#### 算法描述

1. 先对数组从左到右进行冒泡排序(升序),则最大的元素去到最右端.
2. 再对数组从右到左进行冒泡排序(降序),则最小的元素去到最左端.
3. 以此类推,依次改变冒泡的方向,并不断缩小未排序元素的范围,直到最后一个元素结束.

![sorting_shaker_sort_anim][sorting_shaker_sort_anim]

#### 实例分析

* 数组: [45, 19, 77, 81, 13, 28, 18, 19, 77], 排序过程:

```plain
从左到右,找到最大的数 81 ,放到数组末尾:
┌─────────────────────────────────────────┐
│  19   45   77   13   28   18   19   77  │  81
└─────────────────────────────────────────┘

从右到左,找到剩余数组(先框中的部分)中最小的数,放到数组开头:
    ┌────────────────────────────────────┐
13  │  19   45   77   18   28   19   77  │   81
    └────────────────────────────────────┘

从左到右,在剩余数组中找到最大数,放在剩余数组的末尾:
    ┌───────────────────────────────┐
13  │  19   45   18   28   18   77  │   77   81
    └───────────────────────────────┘

从右到左:
         ┌──────────────────────────┐
13   18  │  19   45   18   28   77  │   77   81
         └──────────────────────────┘

从左到右:
         ┌─────────────────────┐
13   18  │  19   18   28   45  │  77   77   81
         └─────────────────────┘

从右到左:
              ┌────────────────┐
13   18   18  │  19   28   45  │  77   77   81
              └────────────────┘

从左到右:
              ┌───────────┐
13   18   18  │  19   28  │  45   77   77   81
              └───────────┘

从右到左:
                   ┌──────┐
13   18   18   19  │  28  │  45   77   77   81
                   └──────┘

```

#### 代码实现

```JavaScript
function shakerSort(array) {
    function swap(array, i, j) {
        var temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
    var length = array.length,
        left = 0,
        right = length - 1,
        lastSwappedLeft = left,
        lastSwappedRight = right,
        i,
        j;
    while (left < right) {
        // 从左到右
        lastSwappedRight = 0;
        for (i = left; i < right; i++) {
            if (array[i] > array[i + 1]) {
                swap(array, i, i + 1);
                lastSwappedRight = i;
            }
        }
        right = lastSwappedRight;
        // 从右到左
        lastSwappedLeft = length - 1;
        for (j = right; left < j; j--) {
            if (array[j - 1] > array[j]) {
                swap(array, j - 1, j)
                lastSwappedLeft = j
            }
        }
        left = lastSwappedLeft;
    }
}
```
***

此文参考于 [bubkoo.com][bubkoo.com],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[bubkoo.com]: http://bubkoo.com/2014/01/17/sort-algorithm/archives/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[insertion_sort]: /2017/02/sort-algorithm-1-insertion-sort/ 'insertion_sort'
[bubble_sort]: /2017/02/sort-algorithm-2-bubble-sort/ 'bubble_sort'
[quick_sort]: /2017/02/sort-algorithm-3-quick-sort/ 'quick_sort'
[selection_sort]: /2017/02/sort-algorithm-4-selection-sort/ 'selection_sort'
[heap_sort]: /2017/02/sort-algorithm-5-heap-sort/ 'heap_sort'
[shell_sort]: /2017/02/sort-algorithm-6-shell-sort/ 'shell_sort'
[merge_sort]: /2017/02/sort-algorithm-7-merge-sort/ 'merge_sort'
[cocktail_sort]: /2017/02/sort-algorithm-8-cocktail-sort/ 'cocktail_sort'
[bogo_sort]: /2017/02/sort-algorithm-9-bogo-sort/ 'bogo_sort'
[bucket_sort]: /2017/02/sort-algorithm-10-bucket-sort/ 'bucket_sort'
[radix_sort]: /2017/02/sort-algorithm-11-radix-sort/ 'radix_sort'

[sorting_shaker_sort_anim]: /assets/images/sort_algorithm/sorting_shaker_sort_anim.gif 'sorting_shaker_sort_anim'