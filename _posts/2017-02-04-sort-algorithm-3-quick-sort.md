---
layout: post
title: 排序算法的详细介绍(三) -- 快速排序
date: 2017-02-04 11:30:00 +08:00
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

### 快速排序 Quick Sort

#### 算法描述

1. 在数据集之中,选择一个元素作为"基准"(pivot).
2. 所有小于"基准"的元素,都移到"基准"的左边;所有大于"基准"的元素,都移到"基准"的右边. 这个操作称为分区(partition)操作,分区操作结束后,基准元素所处的位置就是最终排序后它的位置.
3. 对"基准"左边和右边的两个子集,不断重复第一步和第二步,直到所有子集只剩下一个元素为止.

![Sorting_quicksort_anim][Sorting_quicksort_anim]

#### 实例分析

* 数组: [3, 7, 8, 5, 2, 1, 9, 5, 4], 排序过程:

```plain
首先选定一个基准元素. 这里选元素 5 为基准元素(基准元素可以任意选择):
          pivot
            ↓
3   7   8   5   2   1   9   5   4

将基准元素与数组中最后一个元素交换位置,如果选择最后一个元素为基准元素可以省略该步:
                              pivot
                                ↓
3   7   8   4   2   1   9   5   5

从左到右(除了最后的基准元素),循环移动小于基准元素 5 的所有元素到数组开头,留下大于等于基准元素的元素接在后面.
在这个过程它也为基准元素找寻最后摆放的位置.

循环流程如下:
循环 i = 0 时,storeIndex = 0,找到一个小于基准元素的元素 3:
将其与 storeIndex 所在位置的元素交换位置,这里是 3 自身,交换后将 storeIndex 自增 1,storeIndex = 1:
                                pivot
                                  ↓
  3   7   8   4   2   1   9   5   5
  ↑
storeIndex

循环 i = 3 时,storeIndex = 1,找到一个小于基准元素的元素 4:
     ┌───────┐                 pivot
     ↓       ↓                   ↓
 3   7   8   4   2   1   9   5   5
     ↑       ↑
storeIndex   i

交换位置后,storeIndex 自增 1,storeIndex = 2:
                              pivot
                                ↓
3   4   8   7   2   1   9   5   5
        ↑           
   storeIndex
   
循环 i = 4 时,storeIndex = 2,找到一个小于基准元素的元素 2:
        ┌───────┐             pivot
        ↓       ↓               ↓
3   4   8   7   2   1   9   5   5
        ↑       ↑
   storeIndex   i

交换位置后,storeIndex 自增 1,storeIndex = 3:
                              pivot
                                ↓
3   4   2   7   8   1   9   5   5
            ↑           
       storeIndex

循环 i = 5 时,storeIndex = 3,找到一个小于基准元素的元素 1:
            ┌───────┐         pivot
            ↓       ↓           ↓
3   4   2   7   8   1   9   5   5
            ↑       ↑
       storeIndex   i
       
交换后位置后,storeIndex 自增 1,storeIndex = 4:
                              pivot
                                ↓
3   4   2   1   8   7   9   5   5
                ↑           
           storeIndex

循环 i = 7 时,storeIndex = 4,找到一个小于等于基准元素的元素 5:
                ┌───────────┐ pivot
                ↓           ↓   ↓
3   4   2   1   8   7   9   5   5
                ↑           ↑
           storeIndex       i

交换后位置后,storeIndex 自增 1,storeIndex = 5:
                              pivot
                                ↓
3   4   2   1   5   7   9   8   5
                    ↑           
               storeIndex

循环结束后交换基准元素和 storeIndex 位置的元素的位置:
                  pivot
                    ↓
3   4   2   1   5   5   9   8   7
                    ↑           
               storeIndex

那么 storeIndex 的值就是基准元素的最终位置,这样整个分区过程就完成了.
```

![Quick_sort_Partition_example][Quick_sort_Partition_example]

#### 代码实现

```JavaScript
function quickSort(array) {
	// 交换元素位置
	function swap(array, i, k) {
		var temp = array[i];
		array[i] = array[k];
		array[k] = temp;
	}
	// 数组分区，左小右大
	function partition(array, left, right) {
		var storeIndex = left;        
		var pivot = array[right]; // 直接选最右边的元素为基准元素
		for (var i = left; i < right; i++) {
			if (array[i] < pivot) {
				swap(array, storeIndex, i);
				storeIndex++; // 交换位置后，storeIndex 自增 1，代表下一个可能要交换的位置
			}
		}
		swap(array, right, storeIndex); // 将基准元素放置到最后的正确位置上
		return storeIndex;
	}
	function sort(array, left, right) {
		if (left > right) {
			return;
		}
		var storeIndex = partition(array, left, right);
		sort(array, left, storeIndex - 1);
		sort(array, storeIndex + 1, right);
	}
	sort(array, 0, array.length - 1);
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

[Sorting_quicksort_anim]: /assets/images/sort_algorithm/Sorting_quicksort_anim.gif 'Sorting_quicksort_anim'
[Quick_sort_Partition_example]: /assets/images/sort_algorithm/Quick_sort_Partition_example.png 'Quick_sort_Partition_example'