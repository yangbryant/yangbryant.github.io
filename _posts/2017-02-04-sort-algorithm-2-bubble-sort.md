---
layout: post
title: 排序算法的详细介绍(二) -- 冒泡排序
date: 2017-02-04 11:28:00 +08:00
tags: 温故而知新
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

### 冒泡排序 Bubble Sort

#### 算法描述

1. 比较相邻的元素. 如果第一个比第二个大,就交换他们两个.
2. 对每一对相邻元素作同样的工作,从开始第一对到结尾的最后一对. 在这一点,最后的元素应该会是最大的数.
3. 针对所有的元素重复以上的步骤,除了最后一个.
4. 持续每次对越来越少的元素重复上面的步骤,直到没有任何一对数字需要比较.

![Bubble_sort_animation][Bubble_sort_animation]

#### 实例分析
* 数组: [5, 1, 4, 2, 8], 排序过程:

```plain
第一次外循环
( 5 1 4 2 8 ) → ( 1 5 4 2 8 )   5 > 1 交换位置
  └─┘
( 1 5 4 2 8 ) → ( 1 4 5 2 8 )   5 > 4 交换位置
    └─┘
( 1 4 5 2 8 ) → ( 1 4 2 5 8 )   5 > 2 交换位置
      └─┘
( 1 4 2 5 8 ) → ( 1 4 2 5 8 )   5 < 8 位置不变
        └─┘

第二次外循环（除开最后一个元素8，对剩余的序列）
( 1 4 2 5 8 ) → ( 1 4 2 5 8 )   1 < 4 位置不变
  └─┘
( 1 4 2 5 8 ) → ( 1 2 4 5 8 )   4 > 2 交换位置
    └─┘
( 1 2 4 5 8 ) → ( 1 2 4 5 8 )   4 < 5 位置不变
      └─┘

第三次外循环（除开已经排序好的最后两个元素，可以注意到上面的数组其实已经排序完成，但是程序本身并不知道，所以还要进行后续的循环，直到剩余的序列为 1）
( 1 2 4 5 8 ) → ( 1 2 4 5 8 )   1 < 2 位置不变
  └─┘
( 1 2 4 5 8 ) → ( 1 2 4 5 8 )   2 < 4 位置不变
    └─┘

第四次外循环（最后一次）
( 1 2 4 5 8 ) → ( 1 2 4 5 8 )   1 < 4 位置不变
  └─┘
```

#### 代码实现

```JavaScript
function bubbleSort(array) {
   var length = array.length,
       i,
       j,
       temp;
   for (i = length - 1; 0 < i; i--) {
       for (j = 0; j < i; j++) {
           if (array[j] > array[j + 1]) {
               temp = array[j];
               array[j] = array[j + 1];
               array[j + 1] = temp;
           }
       }
   }
   return array;
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

[Bubble_sort_animation]: /assets/images/sort_algorithm/Bubble_sort_animation.gif 'Bubble_sort_animation'