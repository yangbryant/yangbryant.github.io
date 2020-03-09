---
layout: post
title: 排序算法的详细介绍(四) -- 选择排序
date: 2017-02-04 11:31:00 +08:00
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

### 选择排序 Selection Sort

#### 算法描述

1. 在未排序序列中找到最小(大)元素,存放到排序序列的起始位置(末尾位置).
2. 再从剩余未排序元素中继续寻找最小(大)元素,重复第一步.
3. 以此类推,直到所有元素均排序完毕.

![selection_sort_animation][selection_sort_animation]

#### 实例分析

* 数组: [8, 5, 2, 6, 9, 3, 1, 4, 0, 7], 排序过程:

```plain
第一次从数组 [8, 5, 2, 6, 9, 3, 1, 4, 0, 7] 中找到最小的数 0 ,放到数组的最前面(与第一个元素进行交换):
                               min
                                ↓
8   5   2   6   9   3   1   4   0   7
↑                               ↑
└───────────────────────────────┘

交换后:
0   5   2   6   9   3   1   4   8   7

在剩余的序列中 [5, 2, 6, 9, 3, 1, 4, 8, 7] 中找到最小的数 1 ,与该序列的第一个个元素进行位置交换:
                       min
                        ↓
0   5   2   6   9   3   1   4   8   7
    ↑                   ↑
    └───────────────────┘
    
交换后:	
0   1   2   6   9   3   5   4   8   7

在剩余的序列中 [2, 6, 9, 3, 5, 4, 8, 7] 中找到最小的数 2 ,与该序列的第一个个元素进行位置交换(实际上不需要交换):
       min
        ↓
0   1   2   6   9   3   5   4   8   7
        ↑
        
重复上述过程,直到最后一个元素就完成了排序.

                   min
                    ↓
0   1   2   6   9   3   5   4   8   7
            ↑       ↑
            └───────┘
                           min
                            ↓
0   1   2   3   9   6   5   4   8   7
                ↑           ↑
                └───────────┘
                       min
                        ↓
0   1   2   3   4   6   5   9   8   7
                    ↑   ↑
                    └───┘
                       min
                        ↓
0   1   2   3   4   5   6   9   8   7
                        ↑   
                                   min
                                    ↓
0   1   2   3   4   5   6   9   8   7
                            ↑       ↑
                            └───────┘  
                               min
                                ↓
0   1   2   3   4   5   6   7   8   9
                                ↑      
                                   min
                                    ↓
0   1   2   3   4   5   6   7   8   9
                                    ↑

```

![Selection_Sort_example][Selection_Sort_example]

#### 代码实现

```JavaScript
function selectionSort(array) {
  var length = array.length,
      i,
      j,
      minIndex,
      minValue,
      temp;
  for (i = 0; i < length - 1; i++) {
    minIndex = i;
    minValue = array[minIndex];
    for (j = i + 1; j < length; j++) {
      if (array[j] < minValue) {
        minIndex = j;
        minValue = array[minIndex];
      }
    }
    // 交换位置
    temp = array[i];
    array[i] = minValue;
    array[minIndex] = temp;
  }
  return array
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

[selection_sort_animation]: /assets/images/sort_algorithm/selection_sort_animation.gif 'selection_sort_animation'
[Selection_Sort_example]: /assets/images/sort_algorithm/Selection_Sort_example.gif 'Selection_Sort_example'