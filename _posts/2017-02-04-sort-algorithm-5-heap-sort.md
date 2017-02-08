---
layout: post
title: 排序算法的详细介绍(五) -- 堆排序
date: 2017-02-04 11:32:00 +08:00
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

### 堆排序 Heap Sort

#### 算法描述

* 堆排序是把最大堆堆顶的最大数取出,将剩余的堆继续调整为最大堆,再次将堆顶的最大数取出,这个过程持续到剩余数只有一个时结束.
* 最大堆调整(Max-Heapify): 将堆的末端子节点作调整,使得子节点永远小于父节点.
* 创建最大堆(Build-Max-Heap): 将堆所有数据重新排序,使其成为最大堆.
* 堆排序(Heap-Sort): 移除位在第一个数据的根节点,并做最大堆调整的递归运算.

#### 实例分析

* 先注意一个问题: 数组都是 Zero-Based ,这就意味着我们的堆数据结构模型要发生改变.

![heap_and_array_zero_based][heap_and_array_zero_based]

* 相应的，几个计算公式也要作出相应调整:
1. Parent(i) = floor((i-1)/2), i 的父节点下标,
2. Left(i) = 2i + 1, i 的左子节点下标,
3. Right(i) = 2(i + 1), i 的右子节点下标,

* 最大堆调整(MAX‐HEAPIFY)的作用是保持最大堆的性质, 是创建最大堆的核心子程序, 作用过程如图所示:

![Max_heapify_Procedure][Max_heapify_Procedure]

* 由于一次调整后,堆仍然违反堆性质,所以需要递归的测试,使得整个堆都满足堆性质,用 JavaScript 可以表示如下:

```JavaScript
/**
 * 从 index 开始检查并保持最大堆性质
 *
 * @array
 *
 * @index 检查的起始下标
 *
 * @heapSize 堆大小
 *
 **/
function maxHeapify(array, index, heapSize) {
  var iMax = index,
      iLeft = 2 * index + 1,
      iRight = 2 * (index + 1);
  if (iLeft < heapSize && array[index] < array[iLeft]) {
    iMax = iLeft;
  }
  if (iRight < heapSize && array[iMax] < array[iRight]) {
    iMax = iRight;
  }
  if (iMax != index) {
    swap(array, iMax, index);
    maxHeapify(array, iMax, heapSize); // 递归调整
  }
}
function swap(array, i, j) {
  var temp = array[i];
  array[i] = array[j];
  array[j] = temp;
}
```

* 创建最大堆(Build-Max-Heap)的作用是将一个数组改造成一个最大堆,接受数组和堆大小两个参数,Build-Max-Heap 将自下而上的调用 Max-Heapify 来改造数组,建立最大堆.
* 因为 Max-Heapify 能够保证下标 i 的结点之后结点都满足最大堆的性质,所以自下而上的调用 Max-Heapify 能够在改造过程中保持这一性质.如果最大堆的数量元素是 n,那么 Build-Max-Heap 从 Parent(n) 开始,往上依次调用 Max-Heapify.流程如下:

![building_a_heap][building_a_heap]

* 用 JavaScript 描述如下:

```JavaScript:
function buildMaxHeap(array, heapSize) {
  var i,
      iParent = Math.floor((heapSize - 1) / 2);
      
  for (i = iParent; i >= 0; i--) {
    maxHeapify(array, i, heapSize);
  }
}
```

* 堆排序(Heap-Sort)是堆排序的接口算法,Heap-Sort先调用Build-Max-Heap将数组改造为最大堆,然后将堆顶和堆底元素交换,之后将底部上升,最后重新调用Max-Heapify保持最大堆性质.由于堆顶元素必然是堆中最大的元素,所以一次操作之后,堆中存在的最大元素被分离出堆,重复n-1次之后,数组排列完毕.整个流程如下:

![sort_a_heap][sort_a_heap]

* 用 JavaScript 描述如下:

```JavaScript:
function heapSort(array, heapSize) {
  buildMaxHeap(array, heapSize);
  for (int i = heapSize - 1; i > 0; i--) {
    swap(array, 0, i);
    maxHeapify(array, 0, i);
  }  
}
```

#### 代码实现

```JavaScript
function heapSort(array) {
  function swap(array, i, j) {
    var temp = array[i];
    array[i] = array[j];
    array[j] = temp;
  }
  function maxHeapify(array, index, heapSize) {
    var iMax,
      iLeft,
      iRight;
    while (true) {
      iMax = index;
      iLeft = 2 * index + 1;
      iRight = 2 * (index + 1);
      if (iLeft < heapSize && array[index] < array[iLeft]) {
        iMax = iLeft;
      }
      if (iRight < heapSize && array[iMax] < array[iRight]) {
        iMax = iRight;
      }
      if (iMax != index) {
        swap(array, iMax, index);
        index = iMax;
      } else {
        break;
      }
    }
  }
  function buildMaxHeap(array) {
    var i,
      iParent = Math.floor(array.length / 2) - 1;
    for (i = iParent; i >= 0; i--) {
      maxHeapify(array, i, array.length);
    }
  }
  function sort(array) {
    buildMaxHeap(array);
    for (var i = array.length - 1; i > 0; i--) {
      swap(array, 0, i);
      maxHeapify(array, 0, i);
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

[Sorting_heapsort_anim]: /assets/images/sort_algorithm/Sorting_heapsort_anim.gif 'Sorting_heapsort_anim'
[heap_and_array_zero_based]: /assets/images/sort_algorithm/heap_and_array_zero_based.png 'heap_and_array_zero_based'
[Max_heapify_Procedure]: /assets/images/sort_algorithm/Max_heapify_Procedure.png 'Max_heapify_Procedure.png'
[building_a_heap]: /assets/images/sort_algorithm/building_a_heap.png 'building_a_heap'
[sort_a_heap]: /assets/images/sort_algorithm/sort_a_heap.png 'sort_a_heap'