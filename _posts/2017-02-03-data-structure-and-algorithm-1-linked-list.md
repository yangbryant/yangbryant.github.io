---
layout: post
title: 数据结构与算法基础(一) -- 链表
date: 2017-02-03 09:02:00 +08:00
author: Srefan
catalog: true
tags:
    - 入门
    - 数据结构
---

***

#### 数据结构与算法基础:

[数据结构与算法基础(一) -- 链表][linked_list]  
[数据结构与算法基础(二) -- 树][tree]  
[数据结构与算法基础(三) -- 排序算法][sort]  
[数据结构与算法基础(四) -- 其他][other]  

***

### 链表

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

此文参考于 [hit-alibaba.github.io][hit-alibaba.github.io],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[hit-alibaba.github.io]: https://hit-alibaba.github.io/interview/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[single_linked_list]: /assets/images/data_structure/single_linked_list.png 'single_linked_list'
[double_linked_list]: /assets/images/data_structure/double_linked_list.jpg 'double_linked_list'

[linked_list]: /2017/02/data-structure-and-algorithm-1-linked-list/
[tree]: /2017/02/data-structure-and-algorithm-2-tree/
[sort]: /2017/02/data-structure-and-algorithm-3-sort/
[other]: /2017/02/data-structure-and-algorithm-4-other/