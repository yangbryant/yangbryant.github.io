---
layout: post
title: 排序算法的详细介绍(十) -- 桶排序
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

### 桶排序 Bucket Sort

#### 算法描述

1. 假设待排序的一组数统一的分布在一个范围中,并将这一范围划分成几个子范围,也就是桶.
2. 将待排序的一组数,分档规入这些子桶,并将桶中的数据进行排序.
3. 将各个桶中的数据有序的合并起来.

#### 实例分析

* 数组: [29, 25, 3, 49, 9, 37, 21, 43], 排序过程:

* 数组中最大数为 49 ,先设置 5 个桶,那么每个桶可存放数的范围为: 0~9, 10~19, 20~29, 30~39, 40~49, 然后分别将这些数放人自己所属的桶:

![bucket_sort_step1][bucket_sort_step1]

* 分别对每个桶里面的数进行排序,或者在将数放入桶的同时用插入排序进行排序.最后,将各个桶中的数据有序的合并起来:

![bucket_sort_step2][bucket_sort_step2]

#### 代码实现

```JavaScript
/*
* @array 将要排序的数组
*
* @step 划分桶的步长，比如 step = 5, 表示每个桶存放的数字的范围是 5, 像 -4~1, 0~5, 6~11
*/
function bucketSort(array, step) {
    var result = [],
        bucket = [],
        bucketCount,
        l = array.length,
        i,
        j,
        k,
        s,
        max = array[0],
        min = array[0],
        temp;
    for (i = 1; i < l; i++) {
        if (array[i] > max) {
            max = array[i]
        }
        if (array[i] < min) {
            min = array[i];
        }
    }
    min = min - 1;
    bucketCount = Math.ceil((max - min) / step); // 需要桶的数量
    for (i = 0; i < l; i++) {
        temp = array[i];
        for (j = 0; j < bucketCount; j++) {
            if (temp > (min + step * j) && temp <= (min + step * (j + 1))) { // 判断放入哪个桶
                if (!bucket[j]) {
                    bucket[j] = [];
                }
                // 通过插入排序将数字插入到桶中的合适位置
                s = bucket[j].length;
                if (s > 0) {
                    for (k = s - 1; k >= 0; k--) {
                        if (bucket[j][k] > temp) {
                            bucket[j][k + 1] = bucket[j][k];
                        } else {
                            break;
                        }
                    }
                    bucket[j][k + 1] = temp;
                } else {
                    bucket[j].push(temp);
                }
            }
        }
    }
    for (i = 0; i < bucketCount; i++) { // 循环取出桶中数据
        if (bucket[i]) {
            k = bucket[i].length;
            for (j = 0; j < k; j++) {
                result.push(bucket[i][j]);
            }
        }
    }
    return result;
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

[bucket_sort_step1]: /assets/images/sort_algorithm/bucket_sort_step1.png 'bucket_sort_step1'
[bucket_sort_step2]: /assets/images/sort_algorithm/bucket_sort_step2.png 'bucket_sort_step2'