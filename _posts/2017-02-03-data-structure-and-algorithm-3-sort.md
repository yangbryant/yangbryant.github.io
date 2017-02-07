---
layout: post
title: 数据结构与算法基础(三) -- 排序算法
date: 2017-02-03 09:02:00 +08:00
tags: 温故而知新
---

***

#### 数据结构与算法基础:

[数据结构与算法基础(一) -- 链表][linked_list]  
[数据结构与算法基础(二) -- 树][tree]  
[数据结构与算法基础(三) -- 排序算法][sort]  
[数据结构与算法基础(四) -- 其他][other] 

***

### 排序算法

#### 	排序算法
* 稳定性
* 计算复杂度 (最差表现,平均表现,最好表现)
1. 一般而言,好的表现 O(n*log(n)),最差表现 O(n^2).
2. 对于一个排序理想的表现是 O(n).
3. 所有基于比较的排序的时间复杂度至少是 O(n*log(n)).

#### 常用的排序算法

##### 稳定排序:

* 冒泡排序 (Bubble Sort) -- O(n^2)
* 插入排序 (Insertion Sort) -- O(n^2)
* 桶排序 (Bucket Sort) -- O(n),需要 O(k) 的额外空间
* 计数排序 (Counting Sort) -- O(n+k),需要 O(n+k) 的额外空间
* 合并排序 (Merge Sort) -- O(n*log(n)),需要 O(n) 的额外空间
* 二叉排序树排序 (Binary Tree Sort) -- 期望时间: O(n*log(n)), 最坏时间: O(n^2),需要 O(n) 额外空间
* 基数排序 (Radix Sort) -- O(n-k),需要 O(n) 额外空间

##### 不稳定排序:

* 选择排序 (Selection Sort) -- O(n^2)
* 希尔排序 (Shell Sort) -- O(n*log(n))
* 堆排序 (Heap Sort) -- O(n*log(n))
* 快速排序 (Quick Sort) -- 期望时间: O(n*log(n)), 最坏时间: O(n^2)

##### 排序算法的复杂度:

![排序算法的复杂度情况][sort_list]

#### 常用排序算法详细介绍及原理动图:

* [排序算法的详细介绍(一) -- 插入排序][insertion_sort]  
* [排序算法的详细介绍(二) -- 冒泡排序][bubble_sort]  
* [排序算法的详细介绍(三) -- 快速排序][quick_sort]  
* [排序算法的详细介绍(四) -- 选择排序][selection_sort]  
* [排序算法的详细介绍(五) -- 堆排序][heap_sort]  
* [排序算法的详细介绍(六) -- 希尔排序][shell_sort]  
* [排序算法的详细介绍(七) -- 归并排序][merge_sort]  
* [排序算法的详细介绍(八) -- 鸡尾酒排序][cocktail_sort]  
* [排序算法的详细介绍(九) -- 猴子排序][bogo_sort]  
* [排序算法的详细介绍(十) -- 桶排序][bucket_sort]  
* [排序算法的详细介绍(十一) -- 基数排序][radix_sort]  

***

此文参考于 [hit-alibaba.github.io][hit-alibaba.github.io],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[hit-alibaba.github.io]: https://hit-alibaba.github.io/interview/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[sort_list]: /assets/images/data_structure/sort_list.jpg 'sort_list'

[linked_list]: /2017/02/data-structure-and-algorithm-1-linked-list/
[tree]: /2017/02/data-structure-and-algorithm-2-tree/
[sort]: /2017/02/data-structure-and-algorithm-3-sort/
[other]: /2017/02/data-structure-and-algorithm-4-other/

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