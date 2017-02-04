---
layout: post
title: 排序算法的详细介绍
date: 2017-02-04 11:28:00 +08:00
tags: 温故而知新
---

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

### 冒泡排序 Bubble Sort

#### 算法描述

1. 比较相邻的元素. 如果第一个比第二个大,就交换他们两个.
2. 对每一对相邻元素作同样的工作,从开始第一对到结尾的最后一对. 在这一点,最后的元素应该会是最大的数.
3. 针对所有的元素重复以上的步骤,除了最后一个.
4. 持续每次对越来越少的元素重复上面的步骤,直到没有任何一对数字需要比较.

![Bubble_sort_animation][Bubble_sort_animation]

#### 实例分析
* 数组: [5, 1, 4, 2, 8], 排序过程:

```plain
第一次外循环
( 5 1 4 2 8 ) → ( 1 5 4 2 8 )   5 > 1 交换位置
  └─┘
( 1 5 4 2 8 ) → ( 1 4 5 2 8 )   5 > 4 交换位置
    └─┘
( 1 4 5 2 8 ) → ( 1 4 2 5 8 )   5 > 2 交换位置
      └─┘
( 1 4 2 5 8 ) → ( 1 4 2 5 8 )   5 < 8 位置不变
        └─┘

第二次外循环（除开最后一个元素8，对剩余的序列）
( 1 4 2 5 8 ) → ( 1 4 2 5 8 )   1 < 4 位置不变
  └─┘
( 1 4 2 5 8 ) → ( 1 2 4 5 8 )   4 > 2 交换位置
    └─┘
( 1 2 4 5 8 ) → ( 1 2 4 5 8 )   4 < 5 位置不变
      └─┘

第三次外循环（除开已经排序好的最后两个元素，可以注意到上面的数组其实已经排序完成，但是程序本身并不知道，所以还要进行后续的循环，直到剩余的序列为 1）
( 1 2 4 5 8 ) → ( 1 2 4 5 8 )   1 < 2 位置不变
  └─┘
( 1 2 4 5 8 ) → ( 1 2 4 5 8 )   2 < 4 位置不变
    └─┘

第四次外循环（最后一次）
( 1 2 4 5 8 ) → ( 1 2 4 5 8 )   1 < 4 位置不变
  └─┘
```

#### 代码实现

```JavaScript
function bubbleSort(array) {
   var length = array.length,
       i,
       j,
       temp;
   for (i = length - 1; 0 < i; i--) {
       for (j = 0; j < i; j++) {
           if (array[j] > array[j + 1]) {
               temp = array[j];
               array[j] = array[j + 1];
               array[j + 1] = temp;
           }
       }
   }
   return array;
}
```

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

### 鸡尾酒排序 Cocktail Sort/Shaker Sort

#### 算法描述

1. 先对数组从左到右进行冒泡排序(升序),则最大的元素去到最右端.
2. 再对数组从右到左进行冒泡排序(降序),则最小的元素去到最左端.
3. 以此类推,依次改变冒泡的方向,并不断缩小未排序元素的范围,直到最后一个元素结束.

![sorting_shaker_sort_anim][sorting_shaker_sort_anim]

#### 实例分析

* 数组: [45, 19, 77, 81, 13, 28, 18, 19, 77], 排序过程:

```plain
从左到右,找到最大的数 81 ,放到数组末尾:
┌─────────────────────────────────────────┐
│  19   45   77   13   28   18   19   77  │  81
└─────────────────────────────────────────┘

从右到左,找到剩余数组(先框中的部分)中最小的数,放到数组开头:
    ┌────────────────────────────────────┐
13  │  19   45   77   18   28   19   77  │   81
    └────────────────────────────────────┘

从左到右,在剩余数组中找到最大数,放在剩余数组的末尾:
    ┌───────────────────────────────┐
13  │  19   45   18   28   18   77  │   77   81
    └───────────────────────────────┘

从右到左:
         ┌──────────────────────────┐
13   18  │  19   45   18   28   77  │   77   81
         └──────────────────────────┘

从左到右:
         ┌─────────────────────┐
13   18  │  19   18   28   45  │  77   77   81
         └─────────────────────┘

从右到左:
              ┌────────────────┐
13   18   18  │  19   28   45  │  77   77   81
              └────────────────┘

从左到右:
              ┌───────────┐
13   18   18  │  19   28  │  45   77   77   81
              └───────────┘

从右到左:
                   ┌──────┐
13   18   18   19  │  28  │  45   77   77   81
                   └──────┘

```

#### 代码实现

```JavaScript
function shakerSort(array) {
    function swap(array, i, j) {
        var temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
    var length = array.length,
        left = 0,
        right = length - 1,
        lastSwappedLeft = left,
        lastSwappedRight = right,
        i,
        j;
    while (left < right) {
        // 从左到右
        lastSwappedRight = 0;
        for (i = left; i < right; i++) {
            if (array[i] > array[i + 1]) {
                swap(array, i, i + 1);
                lastSwappedRight = i;
            }
        }
        right = lastSwappedRight;
        // 从右到左
        lastSwappedLeft = length - 1;
        for (j = right; left < j; j--) {
            if (array[j - 1] > array[j]) {
                swap(array, j - 1, j)
                lastSwappedLeft = j
            }
        }
        left = lastSwappedLeft;
    }
}
```

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

[Insertion_sort_animation]: /assets/images/sort_algorithm/Insertion_sort_animation.gif 'Insertion_sort_animation'
[Insertion_sort_example]: /assets/images/sort_algorithm/Insertion_sort_example.gif 'Insertion_sort_example'

[Bubble_sort_animation]: /assets/images/sort_algorithm/Bubble_sort_animation.gif 'Bubble_sort_animation'

[Sorting_quicksort_anim]: /assets/images/sort_algorithm/Sorting_quicksort_anim.gif 'Sorting_quicksort_anim'
[Quick_sort_Partition_example]: /assets/images/sort_algorithm/Quick_sort_Partition_example.png 'Quick_sort_Partition_example'

[selection_sort_animation]: /assets/images/sort_algorithm/selection_sort_animation.gif 'selection_sort_animation'
[Selection_Sort_example]: /assets/images/sort_algorithm/Selection_Sort_example.gif 'Selection_Sort_example'

[Sorting_heapsort_anim]: /assets/images/sort_algorithm/Sorting_heapsort_anim.gif 'Sorting_heapsort_anim'
[heap_and_array_zero_based]: /assets/images/sort_algorithm/heap_and_array_zero_based.png 'heap_and_array_zero_based'
[Max_heapify_Procedure]: /assets/images/sort_algorithm/Max_heapify_Procedure.png 'Max_heapify_Procedure.png'
[building_a_heap]: /assets/images/sort_algorithm/building_a_heap.png 'building_a_heap'
[sort_a_heap]: /assets/images/sort_algorithm/sort_a_heap.png 'sort_a_heap'

[shell_sort_animation]: /assets/images/sort_algorithm/shell_sort_animation.gif 'shell_sort_animation'
[shell_sort_step1]: /assets/images/sort_algorithm/shell_sort_step1.png 'shell_sort_step1'
[shell_sort_step2]: /assets/images/sort_algorithm/shell_sort_step2.png 'shell_sort_step2'
[shell_sort_step3]: /assets/images/sort_algorithm/shell_sort_step3.png 'shell_sort_step3'
[shell_sort_step4]: /assets/images/sort_algorithm/shell_sort_step4.png 'shell_sort_step4'
[shell_sort_step5]: /assets/images/sort_algorithm/shell_sort_step5.png 'shell_sort_step5'

[merge_sort_animation]: /assets/images/sort_algorithm/merge_sort_animation.gif 'merge_sort_animation'
[merge_sort_example]: /assets/images/sort_algorithm/merge_sort_example.gif 'merge_sort_example'

[sorting_shaker_sort_anim]: /assets/images/sort_algorithm/sorting_shaker_sort_anim.gif 'sorting_shaker_sort_anim'

[bucket_sort_step1]: /assets/images/sort_algorithm/bucket_sort_step1.png 'bucket_sort_step1'
[bucket_sort_step2]: /assets/images/sort_algorithm/bucket_sort_step2.png 'bucket_sort_step2'

[radix_sort_step1]: /assets/images/sort_algorithm/radix_sort_step1.png 'radix_sort_step1'
[radix_sort_step2]: /assets/images/sort_algorithm/radix_sort_step2.png 'radix_sort_step2'