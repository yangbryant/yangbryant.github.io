---
layout: post
title: 排序算法的详细介绍(九) -- 猴子排序
date: 2017-02-04 11:36:00 +08:00
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

### 猴子排序 Bogo Sort

* 猴子排序(Bogo Sort)是个既 **不实用** 又 **原始** 的排序算法.

#### 算法描述

* 其原理等同将一堆卡片抛起,落在桌上后检查卡片是否已整齐排列好,若非就再抛一次.

#### 代码实现

```JavaScript
function bogoSort(array) {
    function swap(array, i, j) {
        var temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
    // 随机交换顺序
    function shuffle(array) {
        var i,
            l = array.length;
        for (var i = 0; i < l; i++) {
            var j = Math.floor(Math.random() * l)
            swap(array, i, j)
        }
    }
    // 判断是否已经排好序
    function isSorted(array) {
        var i,
            l = array.length;
        for (var i = 1; i < l; i++) {
            if (array[i - 1] > array[i]) {
                return false;
            }
        }
        return true;
    }
    var sorted = false;
    while (sorted == false) { // 效率低下的位置
        v = shuffle(array);
        sorted = isSorted(array);
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