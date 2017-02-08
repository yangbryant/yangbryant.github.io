---
layout: post
title: 排序算法的详细介绍(十一) -- 基数排序
date: 2017-02-04 11:38:00 +08:00
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

### 基数排序 Radix Sort

#### 算法描述

1. 将所有待比较数值(正整数)统一为同样的数位长度,数位较短的数前面补零.
2. 然后,从最低位开始,依次进行一次排序.
3. 这样从最低位排序一直到最高位排序完成以后,数列就变成一个有序序列.

#### 实例分析

* 数组: [36, 9, 0, 25, 1, 49, 64, 16, 81, 4], 排序过程:

```plain
36   9   0   25   1   49   64   16   81   4
```

* 首先根据 **个位数** 的数值, 按照个位置等于桶编号的方式, 将它们分配至编号0到9的桶子中:

![radix_sort_step1][radix_sort_step1]

```plain
0   1   81   64   4   25   36   16   9   49
```

* 接着按照十位的数值,分别对号入座:

![radix_sort_step2][radix_sort_step2]

```plain
0   1   4   9   16   25   36   49   64   81
```

#### 代码实现

```JavaScript
function radixSort(array) {
    var bucket = [],
        l = array.length,
        loop,
        str,
        i,
        j,
        k,
        t,
        max = array[0];
    for (i = 1; i < l; i++) {
        if (array[i] > max) {
            max = array[i]
        }
    }
    loop = (max + '').length;
    for (i = 0; i < 10; i++) {
        bucket[i] = [];
    }
    for (i = 0; i < loop; i++) {
        for (j = 0; j < l; j++) {
            str = array[j] + '';
            if (str.length >= i + 1) {
                k = parseInt(str[str.length - i - 1]);
                bucket[k].push(array[j]);
            } else { // 高位为 0
                bucket[0].push(array[j]);
            }
        }
        array.splice(0, l);
        for (j = 0; j < 10; j++) {
            t = bucket[j].length;
            for (k = 0; k < t; k++) {
                array.push(bucket[j][k]);
            }
            bucket[j] = [];
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

[radix_sort_step1]: /assets/images/sort_algorithm/radix_sort_step1.png 'radix_sort_step1'
[radix_sort_step2]: /assets/images/sort_algorithm/radix_sort_step2.png 'radix_sort_step2'