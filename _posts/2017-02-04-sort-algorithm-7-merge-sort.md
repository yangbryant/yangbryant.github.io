---
layout: post
title: 排序算法的详细介绍(七) -- 归并排序
date: 2017-02-04 11:34:00 +08:00
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

### 归并排序 Merge Sort

#### 算法描述

1. 把 n 个记录看成 n 个长度为 l 的有序子表.
2. 进行两两归并使记录关键字有序,得到 n/2 个长度为 2 的有序子表.
3. 重复第 2 步直到所有记录归并成一个长度为 n 的有序表为止.

![merge_sort_animation][merge_sort_animation]

#### 实例分析

* 数组: [6, 5, 3, 1, 8, 7, 2, 4], 排序过程:

```plain
将数组分为长度为 2 的子数组,并使每个子数组有序:
[6, 5]  [3, 1]  [8, 7]  [2, 4]
   ↓       ↓       ↓       ↓
[5, 6]  [1, 3]  [7, 8]  [2, 4]

然后再两两合并:
[6, 5, 3, 1]  [8, 7, 2, 4]
      ↓             ↓
[1, 3, 5, 6]  [2, 4, 7, 8]

最后将两个子数组合并:
[6, 5, 3, 1, 8, 7, 2, 4]
            ↓
[1, 2, 3, 4, 5, 6, 7, 8]

```

![merge_sort_example][merge_sort_example]

#### 代码实现

```JavaScript
function mergeSort(array) {
    function sort(array, first, last) {
        first = (first === undefined) ? 0 : first
        last = (last === undefined) ? array.length - 1 : last
        if (last - first < 1) {
            return;
        }
        var middle = Math.floor((first + last) / 2);
        sort(array, first, middle);
        sort(array, middle + 1, last);
        var f = first,
            m = middle,
            i,
            temp;
        while (f <= m && m + 1 <= last) {
            if (array[f] >= array[m + 1]) { // 这里使用了插入排序的思想
                temp = array[m + 1];
                for (i = m; i >= f; i--) {
                    array[i + 1] = array[i];
                }
                array[f] = temp;
                m++
            } else {
                f++
            }
        }
        return array;
    }
    return sort(array);
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

[merge_sort_animation]: /assets/images/sort_algorithm/merge_sort_animation.gif 'merge_sort_animation'
[merge_sort_example]: /assets/images/sort_algorithm/merge_sort_example.gif 'merge_sort_example'