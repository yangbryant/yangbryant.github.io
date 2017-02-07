---
layout: post
title: 关于数据结构与算法的基础知识
date: 2017-02-03 09:02:00 +08:00
tags: 温故而知新
---

***

### 一. 树

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
3. 后序遍历: 右子树->左子树->根节点.
4. 层次遍历: 按层次从上往下,从左向右输出.
* 仅有前序遍历和后序遍历,不能确定一个二叉树,必须有中序遍历才能确定.

![二叉树遍历的实例][tree_Traversal]

```plain
前序遍历的结果: a b de fg c
中序遍历的结果: de b gf a c
后序遍历的结果: ed gf b c a
层次遍历的结果: a bc df eg
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

### 二. 哈希表

* 哈希表, Hash Table, 散列表, 根据 **键值对** (**Key-Value**)直接访问的数据结构.
* 通过把键值对映射到表中一个位置来访问记录,加快查找速度.
* 哈希表的实现需要解决2个问题: **哈希函数** 和 **冲突解决**.
* [哈希表的C语言实现][hash_table_implement].

#### 哈希函数

* 哈希函数,也叫散列函数,对不同输出值得到一个 **固定长度** 的消息摘要.
* 理想的哈希函数对于不同的输入值应该产生不同的结构,同时散列结果应当具有**同一性**(输出值尽量均匀)和**雪崩效应**(微小的输入值变化使得输出值发生巨大的变化).

#### 冲突解决

* 当两个不同的输入值对应一个输出值时,就会产生'**碰撞**',这时候就需要冲突解决.
* 冲突解决的方法:**开放定址法**, **链地址法**, **建立公共溢出区**.使用最多的是 **链地址法**.
* 链地址法基本思路: 为每个 Hash 值建立一个单链表,当发生冲突时,将记录插入到链表中.

> 有 8 个元素 {a, b, c, d, e, f, g, h},采用某种哈希函数得到的地址分别为: {0, 2, 4, 1, 0, 7, 8, 2}, 当哈希表长度为 10 时,采用链地址法解决冲突的哈希表如下图所示:

![哈希表冲突解决实例][hash-table]

***

### 三. 排序算法

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

[常用的排序算法详细请点击这里.][sort_detail]

[排序算法原理动图请点击这里.][sort_algorithm_detail]

***

### 四. 链表

* 链表是一种递归的数据结构,或者为空(NULL),或者是指向一个结点(node)的引用,该节点还有一个元素和一个指向另一条链表的引用.
* 链表的优点: 
1. 插入及删除操作的时间复杂度为O(1)
2. 可以动态的改变大小
* 链表的缺点:
1. 由于链式存储的特性,不具备良好的空间局部性,链表是一种缓存不友好的数据结构.

#### 单链表 (Single-Linked List)

```c++
typedef struct node //定义一个结构体，在c++中也是一个类  
{  
    int val;  
    struct node* pNext;  
}NODE, *pNode; 
```

![single_linked_list][single_linked_list]

```c++
class MyList {

private:
    pNode pHead;
	
public:
    //构造函数，构造一个空链表头指针
    MyList() {
       this.pHead = NULL;
    }

    //析构函数
    ~MyList() {
        while(this->pHead != NULL) {
            pNode pTemp = pHead->pNext;
            delete pHead;
            pHead = pTemp;
        }
    }
    
    //初始化链表
    void Init()  
    {  
        int a;  
        char ans;   
        pNode pTail,pNew;  
          
        do  
        {         
            cout<<"请输入一个节点值：";  
            cin>>a;  
              
            if(pHead == NULL)//判断链表中是否有元素（是否是空链表）  
            {  
                pHead = new NODE;  
                pHead->val = a;  
                pHead->pNext = NULL;  
                  
                pTail = pHead;  
            }     
            else  
            {     
                pTail = pHead;  
                while(pTail->pNext!=NULL)//把pTail移动到尾部  
                {  
                    pTail = pTail->pNext;  
                }  
  
                pNew = new NODE;//new一个新的接点来接受新输入的值  
                pNew->val = a;  
                pNew->pNext = NULL;  
  
                pTail->pNext = pNew;  
                pTail = pTail->pNext;  
            }     
              
            cout<<"继续吗？(Y/N):  ";  
            cin>>ans;  
              
        }while(ans=='Y'||ans=='y');   
    }
    
    int GetNodeCnt()  //获取单链表中元素的个数  
    {  
        int cnt=0;  
        pNode pTemp = pHead;  
  
        while(pTemp!=NULL)  
        {  
            cnt++;  
            pTemp = pTemp->pNext;  
        }  
  
        return cnt;  
    }
    
    void sort()  //把单链表中的元素排序  
    {  
        int n = this->GetNodeCnt();  
        PNODE p1,p2;  
  
        for(int i=0;i<n-1;i++)  
        {  
            p1 = pHead;  
  
            for(int j=0;j<n-1-i;j++)  
            {  
                p2 = p1->pNext;  
  
                if(p1->val < p2->val)  
                {  
                    int k = p1->val;  
                    p1->val = p2->val;  
                    p2->val = k;  
                }  
  
                p1 = p1->pNext;  
            }  
        }  
    }
    
    int Find(int val)  //按输入的值查找方法  
    {  
        int i=0;  
        PNODE pTemp = pHead;  
        while(pTemp != NULL)  
        {  
            if(pTemp->val == val)  
            {  
                return i;  
            }  
            pTemp = pTemp->pNext;  
            i++;  
        }  
        return -1;  
    }  
      
    void Travel()  //遍历单链表中的元素  
    {  
        PNODE pTemp = this->pHead;  
  
        while(pTemp!=NULL)  
        {   
            cout<<pTemp->val<<"  ";  
            pTemp = pTemp->pNext;  
        }  
        cout<<endl;     
    }
}
```
* 插入节点

```c++
void Add(int val) //向链表中追加值方法  
{  
    if(pHead == NULL)
    {  
        pHead = new NODE;  
        pHead->val = val;  
        pHead->pNext = NULL;  
    }  
    else  
    {  
        pNode pTemp = pHead;  
        while(pTemp->pNext!=NULL)  
        {  
            pTemp = pTemp->pNext;  
        }  
  
        pNode pNew = new NODE;
        pNew->val = val;  
        pNew->pNext = NULL;  
  
        pTemp->pNext = pNew;  
    }         
}

int InsertAt(int val,int k)//向链表中插入元素，约定在k之前插入  
{  
    pNode p1,p2,pNew,pTemp;  
    if(pHead == NULL)//链表为空  
    {  
        return -1;  
    }  
  
    if(k<0 || k>this->GetNodeCnt()-1)//k越界  
    {  
        return -1;  
    }  
  
    if(k==0)//在头节点之前插入  
    {  
        pTemp = pHead;  
        pNew = new NODE;  
        pNew->val = val;  
        pNew->pNext = NULL;  
  
        pHead = pNew;  
        pNew->pNext = pTemp;                
        return 0;  
    }
  
    p1 = pHead;  
    int i =0;  
    while(i<k-1)  
    {  
        p1 = p1->pNext;  
        i++;  
    }  
  
    p2 = p1->pNext;    
    pNew = new NODE;  
    pNew->val = val;  
    pNew->pNext = NULL;  
  
    p1->pNext = pNew;  
    pNew->pNext = p2;
    return 0;
}
```

* 删除节点

```c++
int DelAt(int k)   //删除链表中的元素的方法  
{  
    pNode p1,p2,pTemp;  
  
    if(pHead == NULL)  
    {  
        return -1;
    }

    if(k<0 || k>this->GetNodeCnt()-1)  
    {  
        return -1;  
    }  
  
    if(this->GetNodeCnt() == 1)  
    {  
        delete pHead;  
        pHead = NULL;  
        return 0;  
    }

    if(k==0)
    {  
        pTemp = pHead;  
        pHead = pTemp->pNext;  
        delete pTemp;  
        return 0;  
    }  

    if(k == this->GetNodeCnt()-1)  
    {  
        pNode p;  
  
        p = pHead;  
        while(p->pNext->pNext!=NULL)  
        {  
            p = p->pNext;  
        }  
  
        pTemp = p->pNext;  
        p->pNext = NULL;  
        delete pTemp;  
        return 0;  
    }  
  
    p1 = pHead;  
    int i=0;  
    while(i<k-1)  
    {  
        p1 = p1->pNext;  
        i++;  
    }  
  
    pTemp = p1->pNext;  
    p2 = p1->pNext->pNext;  
  
    p1->pNext = p2;  
    delete pTemp;  
    return 0;  
}
```

#### 双向链表 (Double-Linked List)

* 双向链表与单向链表相比, 各个结点多了一个指向前一个结点的指针.
* 双向链表与单向链表相比, 它同时支持高效的正向及反向遍历, 并且可以方便的在链表尾部删除结点.

```c++
struct DoubleList
{
    int nData;
    MyList* pPre;
    MyList* pNext;
};
```

![double_linked_list][double_linked_list]

```c
//全局的变量,保存链表的头
DoubleList* g_pHead = NULL;

//判断链表是否为空
bool IsEmpty()
{
    //如果头指针指向的是空,链表为空
    return g_pHead == NULL;
}

//清空链表
int Clear()
{
    //删除所有数据,并把头指针指向为空
    DoubleList* pTemp = g_pHead;

    //遍历链表的数据.如果有数据,删除,然后进入下一个数据
    while(g_pHead)
    {
        g_pHead = g_pHead->pNext;
        delete pTemp;
        pTemp = g_pHead;
    }

    return 1;
}

//查找数据
bool Find(int nData)
{
    //从头节点开始寻找
    DoubleList* pTemp = g_pHead;
    //遍历数据
    while(pTemp)
    {
        if (pTemp->nData == nData)
        {
            return true;
        }
        pTemp = pTemp->pNext;
    }
    //遍历之后没有找到，则没有该数据，返回false
    return false;
}
```

* 插入节点

```c
//插入数据到链表头
int Insert(int nData)
{
    //这里是插入到头的位置
    //申请一个新的空间存放数据
    DoubleList* pTemp = new DoubleList();
    pTemp->nData = nData;
    pTemp->pPre = NULL;
    //他的next指向头节点,他作为头节点的上一个成为新头节点
    pTemp->pNext = g_pHead;
    if (g_pHead)
    {
        //如果头节点有数据,则把头节点的上一个指向该节点
        g_pHead->pPre = pTemp;
    }
    //是头节点指针指向他,他成为新的头节点
    g_pHead = pTemp;

    return 1;
}
```

* 删除节点

```c
//删除数据
bool Delete(int nData)
{
    //先找到数据,然后再删除
    MyList* pTemp = g_pHead;
    
    //遍历链表找到要删除的节点
    while (pTemp)        
    {
        //找到了,删除它
        if (pTemp->nData == nData)
        {
            if (pTemp->pPre)    
            {
                //如果他有前一个节点,则前一个节点的next指向他的下一个节点,这样就不会吊链了
                pTemp->pPre->pNext = pTemp->pNext;
            }
            else
            {
                //没有上一个节点,则他就是头节点,头节点指针指向他
                g_pHead = pTemp->pNext;
            }

            //处理他的下一个节点情况,和上节点类似处理
            if (pTemp->pNext)
            {
                pTemp->pNext->pPre = pTemp->pPre;
            }

            //删除它的数据空间
            delete pTemp;
            return true;
        }
    }
    //没有找到数据,删除失败,返回false
    return false;
}
```

***

### 五. 搜索

***

### 六. 字符串

***

### 七. 向量/矩阵

***

### 八. 随机

#### 洗牌算法

* 利用一次循环等概率的取到不同的元素.
* 逻辑流程: 如果元素存在于数组中,即可将每次 Random 到的元素 与 最后一个元素 进行交换,然后 count-- .

```c++
#include <iostream>
#include <ctime> 
using namespace std;

const int maxn = 10;

int a[maxn];

int randomInt(int a) {
    return rand()%a;
}
void swapTwoElement(int*x,int*y) {
     int temp;
     temp=*x;
     *x=*y;
     *y=temp;
}

int main(){
    int count = sizeof(a)/sizeof(int);
    int count_b = count;
    srand((unsigned)time(NULL));
    for (int i = 0; i < count; ++i) { a[i] = i; }
    for (int i = 0; i < count_b; ++i) {
        int random = randomInt(count);
        cout<<a[random]<<" ";
        swapTwoElement(&a[random],&a[count-1]);
        count--;
    }
}
```

***

### 九. 贪心

* 在对问题求解时,总是做出在 **当前看来是最好的选择** ,不从 **整体最优** 上考虑,仅仅是 **局部最优解**.
* 基本思路:
1. 建立数学模型来描述问题.
2. 把求解的问题分成若干个子问题.
3. 对每一子问题求解,得到子问题的局部最优解.
4. 把子问题的解局部最优解合成原来解问题的一个解.
* 贪心策略适用的前提是: 局部最优策略能导致产生全局最优解.

***

### 十. 动态规划

***

此文参考于 [hit-alibaba.github.io][hit-alibaba.github.io],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[hash_table_implement]: http://www.cnblogs.com/xiekeli/archive/2012/01/13/2321207.html
[sort_detail]: http://blog.csdn.net/whuslei/article/details/6442755/
[sort_algorithm_detail]: /2017/02/sort-algorithm-1-insertion-sort/ '排序算法'
[hit-alibaba.github.io]: https://hit-alibaba.github.io/interview/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[FullBT_CompleteBT]: /assets/images/data_structure/FullBT_CompleteBT.jpg 'FullBT_CompleteBT'
[tree_Traversal]: /assets/images/data_structure/tree_Traversal.jpg 'tree_Traversal'
[huffman_tree]: /assets/images/data_structure/huffman_tree.jpg 'huffman_tree'
[huffman_tree_calc]: /assets/images/data_structure/Huffman_tree_calc.jpg 'huffman_tree_calc'
[hash-table]: /assets/images/data_structure/hash-table.jpg 'hash-table'
[sort_list]: /assets/images/data_structure/sort_list.jpg 'sort_list'

[single_linked_list]: /assets/images/data_structure/single_linked_list.png 'single_linked_list'
[double_linked_list]: /assets/images/data_structure/double_linked_list.jpg 'double_linked_list'