---
layout: post
title: 数据结构与算法基础(二) -- 树
date: 2017-02-03 09:03:00 +08:00
tags: 温故而知新
---

***

#### 数据结构与算法基础:

[数据结构与算法基础(一) -- 链表][linked_list]  
[数据结构与算法基础(二) -- 树][tree]  
[数据结构与算法基础(三) -- 排序算法][sort]  
[数据结构与算法基础(四) -- 其他][other] 

***

### 树

#### 二叉树

* 有限个节点的集合,由一个 **根节点** 和 **两条互不相关的二叉树** 组成.

```C
typedef struct BinaryTreeNode
{
    int m_nValue;
    BinaryTreeNode* m_pLeft;
    BinaryTreeNode* m_pRight;
    
} BinaryTreeNode;
```

* 二叉树的性质:
1. 二叉树中第 **i** 层节点数 **最多** 为 **2^(i-1)**.
2. 深度为 **k** 的二叉树节点总数 **最多** 为 **2^k-1**.
3. 具有 **n** 个节点的完全二叉树的深度为 **log2(n) +1**.

* 完全二叉树和满二叉树如下:

![完全二叉树和满二叉树][FullBT_CompleteBT]

* 二叉树的遍历:前序遍历,中序遍历,后序遍历.
1. 前序遍历: 根节点->左子树->右子树.
2. 中序遍历: 左子树->根节点->右子树.
3. 后序遍历: 左子树->右子树->根节点.
4. 层次遍历: 按层次从上往下,从左向右输出.
* 仅有前序遍历和后序遍历,不能确定一个二叉树,必须有中序遍历才能确定.

![二叉树遍历的实例][tree_Traversal]

```plain
前序遍历的结果: a b de fg c
中序遍历的结果: de b gf a c
后序遍历的结果: ed gf b c a
层次遍历的结果: a bc df eg
```

```c
// 前序遍历
void pre_order_traversal(TreeNode* root){
  //Do Something with root
  if(root->lchild!=NULL)
    pre_order_traversal(root->lchild);
  if(root->rchild!=NULL)
    pre_order_traversal(root->rchild);
}

// 中序遍历
void in_order_traversal(TreeNode* root){
  if(root->lchild!=NULL)
    in_order_traversal(root->lchild);
  //Do Something with root
  if(root->rchild!=NULL)
    in_order_traversal(root->rchild);
}

// 后序遍历
void post_order_traversal(TreeNode* root){
  if(root->lchild!=NULL)
    post_order_traversal(root->lchild);
  if(root->rchild!=NULL)
    post_order_traversal(root->rchild);
  //Do Something with root
}
```
 
#### 堆

* 最大堆: 完全二叉树的 **任意一个非终端节点的元素** 都 **不小于** 其 **左儿子节点** 和 **右儿子节点** (如果存在) 的元素, 则称此 **完全二叉树为最大堆**.
* 最小堆: 完全二叉树的 **任意一个非终端节点的元素** 都 **不大于** 其 **左儿子节点** 和 **右儿子节点** (如果存在) 的元素, 则称此 **完全二叉树为最小堆**.
* 最大堆的 **根节点** 的元素是整个堆中 **最大的**.
* 最小堆的 **根节点** 的元素是整个堆中 **最小的**.

#### 哈弗曼树

* Huffman Tree,又称 **最优树**.
* 给定n个权值作为n个叶子节点,构造一个二叉树,带权路径长度最小为最优二叉树,即哈弗曼树.

![哈弗曼树实例][huffman_tree]

```plain
图a: 带权路径长度: WPL = 5*2 + 7*2 + 2*2 + 13*2 = 54
图b: 带权路径长度: WPL = 5*3 + 2*3 + 7*2 + 13*1 = 48 -- 此为哈弗曼树
```

* 如何构建哈弗曼树:

![哈弗曼树的计算][huffman_tree_calc]

1. 把所有的左右子树为空的作为根节点,组成森林.
2. 从森林中选出两棵根节点权值最小的树作为一棵新树的左右子树,置新树的附加根节点权值为其左,右子树上根节点权值之和.**左子树的权值应小于右子树的权值**.
3. 从森林里删除这两棵树,新树加入到森林中.
4. 重复2,3步骤,直到森林中只有一棵树为止,此树便是哈弗曼树.

#### 二叉排序树

* 二叉排序树, Binary Sort Tree, 又称 **二叉查找树**, Binary Search Tree.
* 二叉排序树或者是一棵空树,或者是具有如下性质的二叉树:
1. 若左子树不空,则左子树上所有节点的值均 **小于** 根节点的值.
2. 若右子树不空,则右子树上所有节点的值均 **大于或者等于** 根节点的值.
3. 左右子树也分别为二叉排序树.
4. **没有键值相等** 的节点.
* 二分查找的时间复杂度为O(log(n)),最坏情况下时间复杂度为O(n).
* 缺点:树的结构是无法预料的,随意性很大,它只与节点的值和插入的顺序有关系,往往得到的是一个不平衡的二叉树.在最坏的情况下,可能得到的是一个单支二叉树,其高度和节点数相同,相当于一个单链表,对其正常的时间复杂度有O(log(n))变成了O(n),从而丧失了二叉排序树的一些应该有的优点.

#### 平衡二叉树

* 平衡二叉树, Balanced Binary Tree, 又称 AVL树.
* 平衡二叉树或者是一棵空树,或者是具有如下性质的二叉树:
1. 左右子树也分别是平衡二叉树.
2. 左右子树的 **深度之差** 绝对值不超过1.
* 平衡二叉树是对 **二叉排序树** 的改进.

#### B-树

* 是一种非二叉的查找树.除了满足树的特性,还要满足如下:
1. 一棵 m 阶的B-树, 树的根或者是一片叶子(一个节点的树),或者其儿子数在 2 和 m 之间.
2. 一棵 m 阶的B-树, 除根外,所有的非叶子结点的孩子数在 m/2 和 m 之间.
3. 一棵 m 阶的B-树, 所有的叶子结点都在相同的深度.
* B-树的平均深度为logm/2(N). 执行查找的平均时间为O(logm).

#### Trie 树

* Trie 树,又称前缀树,字典树,是一种有序树,用于保存关联数组,其中的键通常是字符串.
* 与二叉查找树不同,键不是直接保存在节点中,而是由节点在树中的位置决定.一个节点的所有子孙都有相同的前缀,也就是这个节点对应的字符串,而根节点对应空字符串.一般情况下,不是所有的节点都有对应的值,只有叶子节点和部分内部节点所对应的键才有相关的值.
* Trie 树查询和插入时间复杂度都是 O(n),是一种以空间换时间的方法.当节点树较多的时候,Trie 树占用的内存会很大.
* Trie 树常用于搜索提示.如当输入一个网址,可以自动搜索出可能的选择.当没有完全匹配的搜索结果,可以返回前缀最相似的可能.

***

此文参考于 [hit-alibaba.github.io][hit-alibaba.github.io],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[hit-alibaba.github.io]: https://hit-alibaba.github.io/interview/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[FullBT_CompleteBT]: /assets/images/data_structure/FullBT_CompleteBT.jpg 'FullBT_CompleteBT'
[tree_Traversal]: /assets/images/data_structure/tree_Traversal.jpg 'tree_Traversal'
[huffman_tree]: /assets/images/data_structure/huffman_tree.jpg 'huffman_tree'
[huffman_tree_calc]: /assets/images/data_structure/Huffman_tree_calc.jpg 'huffman_tree_calc'

[linked_list]: /2017/02/data-structure-and-algorithm-1-linked-list/
[tree]: /2017/02/data-structure-and-algorithm-2-tree/
[sort]: /2017/02/data-structure-and-algorithm-3-sort/
[other]: /2017/02/data-structure-and-algorithm-4-other/