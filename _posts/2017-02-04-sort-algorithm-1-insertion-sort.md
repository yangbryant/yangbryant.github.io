---
layout: post
title: 排序算法的详细介绍(一) -- 插入排序
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

### 插入排序 Insertion Sort

#### 算法描述

1. 从第一个元素开始,该元素可以认为已经被排序.
2. 取出下一个元素,在已经排序的元素序列中从后向前扫描.
3. 如果该元素(已排序)大于新元素,将该元素移到下一位置.
4. 重复步骤 3,直到找到已排序的元素小于或者等于新元素的位置.
5. 将新元素插入到该位置后.
6. 重复步骤 2~5.

* 如果比较操作的代价比交换操作大的话,可以采用二分查找法来减少比较操作的数目.该算法可以认为是插入排序的一个变种,称为 **二分查找排序**.

![Insertion_sort_animation][Insertion_sort_animation]

#### 实例分析

* 数组: [5, 6, 3, 1, 8, 7, 2, 4], 排序过程:

```plain

[5]   6   3   1   8   7   2   4
  ↑   │
  └───┘
[5, 6]   3   1   8   7   2   4
↑        │
└────────┘
[3, 5, 6]  1   8   7   2   4
↑          │
└──────────┘
[1, 3, 5, 6]  8   7   2   4
           ↑  │
           └──┘
[1, 3, 5, 6, 8]  7   2   4
            ↑    │
            └────┘
[1, 3, 5, 6, 7, 8]  2   4
   ↑                │
   └────────────────┘
[1, 2, 3, 5, 6, 7, 8]  4
         ↑             │
         └─────────────┘
 
[1, 2, 3, 4, 5, 6, 7, 8]

```

![Insertion_sort_example][Insertion_sort_example]

#### 代码实现

```JavaScript
function insertionSort(array) {
  var length = array.length,
    i,
    j,
    temp;
  for (i = 1; i < length; i++) {
    temp = array[i];
    for (j = i; j >= 0; j--) {
      if (array[j - 1] > temp) {
        array[j] = array[j - 1];
      } else {
        array[j] = temp;
        break;
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

[Insertion_sort_animation]: /assets/images/sort_algorithm/Insertion_sort_animation.gif 'Insertion_sort_animation'
[Insertion_sort_example]: /assets/images/sort_algorithm/Insertion_sort_example.gif 'Insertion_sort_example'