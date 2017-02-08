---
layout: post
title: 排序算法的详细介绍(六) -- 希尔排序
date: 2017-02-04 11:33:00 +08:00
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

### 希尔排序 Shell Sort

#### 算法描述

1. 先取一个正整数 d1(d1 < n),把全部记录分成 d1 个组,所有距离为 d1 的倍数的记录看成一组,然后在各组内进行插入排序.
2. 然后取 d2(d2 < d1).
3. 重复上述分组和排序操作;直到取 di = 1(i >= 1) 位置,即所有记录成为一个组,最后对这个组进行插入排序.
4. 一般选 d1 约为 n/2, d2 为 d1 /2, d3 为 d2/2 ,…, di = 1.

![shell_sort_animation][shell_sort_animation]

#### 实例分析

* 数组: [80, 93, 60, 12, 42, 30, 68, 85, 10], 排序过程:

* 首先取 d1 = 4, 将数组分为 4 组, 如下图中相同颜色代表一组:
![shell_sort_step1][shell_sort_step1]

* 然后分别对 4 个小组进行插入排序, 排序后的结果为:
![shell_sort_step2][shell_sort_step2]

* 然后，取 d2 = 2, 将原数组分为 2 小组:
![shell_sort_step3][shell_sort_step3]

* 然后分别对 2 个小组进行插入排序, 排序后的结果为:
![shell_sort_step4][shell_sort_step4]

* 最后, 取 d3 = 1, 进行插入排序后得到最终结果:
![shell_sort_step5][shell_sort_step5]

#### 代码实现

```JavaScript
function shellSort(array) {
    function swap(array, i, k) {
        var temp = array[i];
        array[i] = array[k];
        array[k] = temp;
    }
    var length = array.length,
        gap = Math.floor(length / 2);
    while (gap > 0) {
        for (var i = gap; i < length; i++) {
            for (var j = i; 0 < j; j -= gap) {
                if (array[j - gap] > array[j]) {
                    swap(array, j - gap, j);
                } else {
                    break;
                }
            }
        }
        gap = Math.floor(gap / 2);
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

[shell_sort_animation]: /assets/images/sort_algorithm/shell_sort_animation.gif 'shell_sort_animation'
[shell_sort_step1]: /assets/images/sort_algorithm/shell_sort_step1.png 'shell_sort_step1'
[shell_sort_step2]: /assets/images/sort_algorithm/shell_sort_step2.png 'shell_sort_step2'
[shell_sort_step3]: /assets/images/sort_algorithm/shell_sort_step3.png 'shell_sort_step3'
[shell_sort_step4]: /assets/images/sort_algorithm/shell_sort_step4.png 'shell_sort_step4'
[shell_sort_step5]: /assets/images/sort_algorithm/shell_sort_step5.png 'shell_sort_step5'