---
layout: post
title: 操作系统基础(三) -- 并发技术
date: 2017-02-08 15:08:00 +08:00
author: Srefan
catalog: true
tags:
    - 入门
    - 操作系统
---

***

#### 操作系统基础:

[操作系统基础(一) -- 数据单元][arch]  
[操作系统基础(二) -- 中断和系统调用][interrupt_and_syscall]  
[操作系统基础(三) -- 并发技术][concurrency]  
[操作系统基础(四) -- 内存管理基础][memory_management]  
[操作系统基础(五) -- 磁盘与文件][disk_and_file]  

***

### 多任务

* **多道程序(Multiprogramming)**: 
1. (上古时代) CPU只能运行一个程序,当CPU空闲下来(例如等待I/O时),CPU就空闲下来了.为了让CPU得到更好的利用,用监控程序发现某个程序暂时无须使用CPU时,就把另外的正在等待CPU资源的程序启动起来,以充分利用CPU资源.
2. 程序之间不区分轻重缓急,对于交互式程序来说,对于CPU计算时间的需求并不多,但是对于响应速度却有比较高的要求.

* **分时系统(Time-Sharing System)**:
1. 改进了多道程序,使得每个程序运行一段时间之后,都主动让出CPU资源,这样每个程序在一段时间内都有机会运行一小段时间.这种程序协作方式被称为 **分时系统**.
2. 对于响应速度要求低,但是需要长时间的CPU计算.
3. 如果某个程序出现了错误,导致了死循环,不仅仅是这个程序会出错,整个系统都会死机.

* **多任务系统(Multi-tasking System)**:
1. 为了避免分时系统死机的问题,出现了更为先进的操作系统模式--多任务系统.
2. 操作系统从最底层接管了所有硬件资源.所有的应用程序在操作系统之上以 **进程(Process)** 的方式运行,每个进程都有自己独立的地址空间,相互隔离. CPU由操作系统统一进行分配.每个进程都有机会得到CPU,同时在操作系统控制之下,如果一个进程运行超过了一定时间,就会被暂停掉,失去CPU资源.
3. 这样就避免了一个程序的错误导致整个系统死机.如果操作系统分配给各个进程的运行时间都很短,CPU可以在多个进程间快速切换,就像很多进程都同时在运行的样子.
4. 几乎所有现代操作系统都是采用这样的方式支持多任务,例如 Unix, Linux, Windows 以及 macOS.

***

### 进程

* 进程是一个具有独立功能的程序关于某个数据集合的一次运行活动.
* 它可以申请和拥有系统资源,是一个动态的概念,是一个活动的实体.它不只是程序的代码,还包括当前的活动,通过程序计数器的值和处理寄存器的内容来表示.

* 进程的概念主要有两点:
1. 进程是一个实体. 每一个进程都有它自己的地址空间,一般情况下,包括文本区域(text region),数据区域(data region)和堆栈(stack region).文本区域存储处理器执行的代码,数据区域存储变量和进程执行期间使用的动态分配的内存,堆栈区域存储着活动过程调用的指令和本地变量.
2. 进程是一个“执行中的程序”. 程序是一个没有生命的实体,只有处理器赋予程序生命时,它才能成为一个活动的实体,我们称其为进程.

#### 进程的基本状态

1. 等待态: 等待某个事件的完成.
2. 就绪态: 等待系统分配处理器以便运行.
3. 运行态: 占有处理器正在运行.

* 运行态 → 等待态: 往往是由于等待外设,等待主存等资源分配或等待人工干预而引起的.
* 等待态 → 就绪态: 则是等待的条件已满足,只需分配到处理器后就能运行.
* 运行态 → 就绪态: 不是由于自身原因,而是由外界原因使运行状态的进程让出处理器,这时候就变成就绪态.例如时间片用完,或有更高优先级的进程来抢占处理器等.
* 就绪态 → 运行态: 系统按某种策略选中就绪队列中的一个进程占用处理器,此时就变成了运行态.

#### 进程调度

##### 调度种类

* 高级调度: (High-Level Scheduling)又称为作业调度,它决定把后备作业调入内存运行.
* 中级调度: (Intermediate-Level Scheduling)又称为在虚拟存储器中引入,在内、外存对换区进行进程对换.
* 低级调度: (Low-Level Scheduling)又称为进程调度,它决定把就绪队列的某进程获得CPU.

##### 非抢占式调度与抢占式调度

* 非抢占式: 分派程序一旦把处理机分配给某进程后便让它一直运行下去,直到进程完成或发生进程调度某事件而阻塞时,才把处理机分配给另一个进程.
* 抢占式: 操作系统将正在运行的进程强行暂停,由调度程序将CPU分配给其他就绪进程的调度方式.

##### 调度策略的设计

* 响应时间: 从用户输入到产生反应的时间.
* 周转时间: 从任务开始到任务结束的时间.
* CPU任务可以分为交互式任务和批处理任务,调度最终的目标是合理的使用CPU,使得交互式任务的响应时间尽可能短,用户不至于感到延迟,同时使得批处理任务的周转时间尽可能短,减少用户等待的时间.

##### 调度算法

* FIFO (First Come First Served, FCFS, 先来先服务调度)
1. 调度的顺序就是任务到达就绪队列的顺序.
2. 公平,简单(FIFO队列),非抢占,不适合交互式.未考虑任务特性,平均等待时间可以缩短.

* Shortest Job First (SJF, 短作业优先调度)
1. 最短的作业(CPU区间长度最小)最先调度,可以保证最小的平均等待时间.
2. Shortest Remaining Job First (SRJF),SJF的可抢占版本，比SJF更有优势. SJF(SRJF): 如何知道下一CPU区间大小? 根据历史进行预测: 指数平均法.

* 优先权调度
1. 每个任务关联一个优先权,调度优先权最高的任务.
2. 注意:优先权太低的任务一直就绪,得不到运行,出现“饥饿”现象.
3. FCFS是RR的特例,SJF是优先权调度的特例.这些调度算法都不适合于交互式系统.

* Round-Robin(RR, 时间片轮转调度)
1. 设置一个时间片,按时间片来轮转调度.
2. 优点: 定时有响应,等待时间较短, 缺点: 上下文切换次数较多.
3. 时间片太大,响应时间太长; 吞吐量变小,周转时间变长; 当时间片过长时,退化为FCFS.

* 多级队列调度
1. 按照一定的规则建立多个进程队列.
2. 不同的队列有固定的优先级(高优先级有抢占权).
3. 不同的队列可以给不同的时间片和采用不同的调度方法.
4. 问题: a.没法区分I/O bound和CPU bound, b.也存在一定程度的“饥饿”现象.

* 多级反馈队列
1. 在多级队列的基础上,任务可以在队列之间移动,更细致的区分任务.
2. 可以根据“享用”CPU时间多少来移动队列,阻止“饥饿”.
3. **最通用的调度算法**: 多数OS都使用该方法或其变形,如UNIX、Windows等.

#### 进程同步

##### 临界资源与临界区

* 在操作系统中, **进程是占有资源的最小单位** (线程可以访问其所在进程内的所有资源,但线程本身并不占有资源或仅仅占有一点必须资源).
* 但对于某些资源来说,其在同一时间只能被一个进程所占用.这些一次只能被一个进程所占用的资源就是所谓的临界资源.
* 典型的临界资源:物理上的打印机,或是存在硬盘或内存中被多个进程所共享的一些变量和数据等.
* 如果临界资源不作为临界资源加以保护,那么很有可能造成丢数据的问题.
* 对于临界资源的访问,必须是互斥进行.也就是当临界资源被占用时,另一个申请临界资源的进程会被阻塞,直到其所申请的临界资源被释放.而进程内访问临界资源的代码被成为 **临界区**.

* 临界区的访问过程:
1. 进入区: 查看临界区是否可访问,如果可以访问,则转到步骤二,否则进程会被阻塞.
2. 临界区: 在临界区做操作.
3. 退出区: 清除临界区被占用的标志.
4. 剩余区: 进程与临界区不相关部分的代码.

* 解决临界区问题可能的方法:
1. 一般软件方法
2. 关中断方法
3. 硬件原子指令方法
4. 信号量方法

##### 信号量

* 信号量是一个确定的二元组(s, q),其中s是一个具有非负初值的整形变量,q是一个初始状态为空的队列,整形变量s表示系统中某类资源的数目:
1. 当其值 ≥ 0 时,表示系统中当前可用资源的数目.
2. 当其值 ＜ 0 时,其绝对值表示系统中因请求该类资源而被阻塞的进程数目.

* 除信号量的初值外,信号量的值仅能由P操作和V操作更改,操作系统利用它的状态对进程和资源进行管理
* P操作:

```plain
P操作记为P(s),其中s为一信号量,它执行时主要完成以下动作:
s.value = s.value - 1;  /*可理解为占用1个资源，若原来就没有则记帐“欠”1个*/

若s.value ≥ 0,则进程继续执行,否则(即s.value < 0),则进程被阻塞,并将该进程插入到信号量s的等待队列s.queue中
说明:实际上,P操作可以理解为分配资源的计数器,或是使进程处于等待状态的控制指令
```

* V操作:

```plain
V操作记为V(s),其中s为一信号量,它执行时,主要完成以下动作:
s.value = s.value + 1;  /*可理解为归还1个资源，若原来就没有则意义是用此资源还1个欠帐*/

若s.value > 0,则进程继续执行,否则(即s.value ≤ 0),则从信号量s的等待队s.queue中移出第一个进程,使其变为就绪状态,然后返回原进程继续执行
说明:实际上,V操作可以理解为归还资源的计数器,或是唤醒进程使其处于就绪状态的控制指令
```

* 信号量方法实现: 生产者 − 消费者互斥与同步控制

```c
semaphore fullBuffers = 0; /*仓库中已填满的货架个数*/
semaphore emptyBuffers = BUFFER_SIZE;/*仓库货架空闲个数*/
semaphore mutex = 1; /*生产-消费互斥信号*/

Producer() 
{ 
    while(True)
    {  
       /*生产产品item*/
       emptyBuffers.P(); 
       mutex.P(); 
       /*item存入仓库buffer*/
       mutex.V();
       fullBuffers.V();
    }
}

Consumer() 
{
    while(True)
    {
        fullBuffers.P(); 
        mutex.P();    
        /*从仓库buffer中取产品item*/
        mutex.V();
        emptyBuffers.V();
        /*消费产品item*/
    }
}
```

* 使用pthread实现的生产者－消费者模型:

```c
#include <pthread.h>
#include <stdio.h>

#include <stdlib.h>
#define BUFFER_SIZE 10

static int buffer[BUFFER_SIZE] = { 0 };
static int count = 0;

pthread_t consumer, producer;
pthread_cond_t cond_producer, cond_consumer;
pthread_mutex_t mutex;

void* consume(void* _){
  while(1){
    pthread_mutex_lock(&mutex);
    while(count == 0){
      printf("empty buffer, wait producer\n");
      pthread_cond_wait(&cond_consumer, &mutex); 
    }

    count--;
    printf("consume a item\n");
    pthread_mutex_unlock(&mutex);
    pthread_cond_signal(&cond_producer);
    //pthread_mutex_unlock(&mutex);
  }
  pthread_exit(0);
}

void* produce(void* _){
  while(1){
    pthread_mutex_lock(&mutex);
    while(count == BUFFER_SIZE){
      printf("full buffer, wait consumer\n");
      pthread_cond_wait(&cond_producer, &mutex);
    }

    count++;
    printf("produce a item.\n");
    pthread_mutex_unlock(&mutex);
    pthread_cond_signal(&cond_consumer);
    //pthread_mutex_unlock(&mutex);
  }
  pthread_exit(0);
}

int main() {

  pthread_mutex_init(&mutex, NULL);
  pthread_cond_init(&cond_consumer, NULL);
  pthread_cond_init(&cond_producer, NULL);

  int err = pthread_create(&consumer, NULL, consume, (void*)NULL);
  if(err != 0){
    printf("consumer thread created failed\n");
    exit(1);
  }

  err = pthread_create(&producer, NULL, produce, (void*)NULL);
  if(err != 0){
    printf("producer thread created failed\n");
    exit(1);
  }

  pthread_join(producer, NULL);
  pthread_join(consumer, NULL);  

  //sleep(1000);

  pthread_cond_destroy(&cond_consumer);
  pthread_cond_destroy(&cond_producer);
  pthread_mutex_destroy(&mutex);


  return 0;
}
```

##### 死锁

* 多个进程因循环等待资源而造成无法执行的现象
* 死锁会造成进程无法执行,同时会造成系统资源的极大浪费(资源无法释放).

* 死锁产生的4个必要条件:
1. 互斥使用 (Mutual exclusion): 指进程对所分配到的资源进行排它性使用,即在一段时间内某资源只由一个进程占用.如果此时还有其它进程请求资源,则请求者只能等待,直至占有资源的进程用毕释放.
2. 不可抢占 (No preemption): 指进程已获得的资源,在未使用完之前,不能被剥夺,只能在使用完时由自己释放.
3. 请求和保持 (Hold and wait): 指进程已经保持至少一个资源,但又提出了新的资源请求,而该资源已被其它进程占有,此时请求进程阻塞,但又对自己已获得的其它资源保持不放.
4. 循环等待 (Circular wait): 指在发生死锁时,必然存在一个进程——资源的环形链,即进程集合{P0, P1, P2, ..., Pn}中的P0正在等待一个P1占用的资源,P1正在等待P2占用的资源,...,Pn正在等待已被P0占用的资源.

* 死锁避免 —— 银行家算法: 判断此次请求是否造成死锁若会造成死锁,则拒绝该请求.

#### 进程间通信

* 本地进程间通信的方式有很多,可以总结为下面四类:
1. 消息传递 (管道, FIFO, 消息队列)
2. 同步 (互斥量, 条件变量, 读写锁, 文件和写记录锁, 信号量)
3. 共享内存 (匿名的和具名的)
4. 远程过程调用 (Solaris门和Sun RPC)

***

### 线程

* 很多时候不同的程序 **需要共享同样的资源(文件,信号量等)** ,如果全都 **使用进程的话会导致切换的成本很高** ,造成CPU资源的浪费.于是就出现了 **线程** 的概念.
* 线程,又称轻量级进程(Lightweight Process, LWP), 是 **程序执行流的最小单元**.
* 一个标准的线程由 **线程ID**, **当前指令指针(PC)**, **寄存器集合** 和 **堆栈** 组成.

* 线程具有以下属性:
1. 轻型实体: 线程中的实体基本上不拥有系统资源,只是有一点必不可少的,能保证独立运行的资源.线程的实体包括程序,数据和TCB. 线程是动态概念,它的动态特性由线程控制块TCB(Thread Control Block)描述.
2. 独立调度和分派的基本单位: 在多线程OS中,线程是能独立运行的基本单位,因而也是独立调度和分派的基本单位.由于线程很“轻”,故线程的切换非常迅速且开销小(在同一进程中的).
3. 可并发执行: 在一个进程中的多个线程之间,可以并发执行,甚至允许在一个进程中所有线程都能并发执行;同样,不同进程中的线程也能并发执行,充分利用和发挥了处理机与外围设备并行工作的能力.
4. 共享进程资源: 在同一进程中的各个线程,都可以共享该进程所拥有的资源,这首先表现在: a.所有线程都具有相同的地址空间(进程的地址空间),线程可以访问该地址空间的每一个虚地址; b.可以访问进程所拥有的已打开文件,定时器,信号量等.由于同一个进程内的线程共享内存和文件,所以线程之间互相通信不必调用内核. 线程共享的环境包括: a.进程代码段, b.进程的公有数据(利用这些共享的数据,线程很容易的实现相互之间的通讯), c.进程打开的文件描述符,信号的处理器,进程的当前目录, d.进程用户ID与进程组ID.

***

### 协程

* 协程 (微线程, 纤程, Coroutine)
* 协程可以理解为用户级线程.
* 协程和线程的区别是: 线程是抢占式的调度, 而协程是协同式的调度, 协程避免了无意义的调度, 由此可以提高性能, 但也因此, 程序员必须自己承担调度的责任, 同时,协程也失去了标准线程使用多CPU的能力.

* 使用协程(python)改写生产者-消费者问题: (可以看到,使用协程不再需要显式地对锁进行操作).

```python
import time

def consumer():
    r = ''
    while True:
        n = yield r
        if not n:
            return
        print('[CONSUMER] Consuming %s...' % n)
        time.sleep(1)
        r = '200 OK'

def produce(c):
    next(c)                   #python 3.x中使用next(c)，python 2.x中使用c.next()
    n = 0
    while n < 5:
        n = n + 1
        print('[PRODUCER] Producing %s...' % n)
        r = c.send(n)
        print('[PRODUCER] Consumer return: %s' % r)
    c.close()

if __name__=='__main__':
    c = consumer()
    produce(c)
```

***

### IO多路复用

* IO多路复用是指内核一旦发现进程指定的一个或者多个IO条件准备读取,它就通知该进程.
* IO多路复用适用如下场合:
1. 当客户处理多个描述字时(一般是交互式输入和网络套接口),必须使用I/O复用.
2. 当一个客户同时处理多个套接口时,而这种情况是可能的,但很少出现.
3. 如果一个TCP服务器既要处理监听套接口,又要处理已连接套接口,一般也要用到I/O复用.
4. 如果一个服务器即要处理TCP,又要处理UDP,一般要使用I/O复用.
5. 如果一个服务器要处理多个服务或多个协议,一般要使用I/O复用.
* 与多进程和多线程技术相比,I/O多路复用技术的最大优势是系统开销小,系统不必创建进程/线程,也不必维护这些进程/线程,从而大大减小了系统的开销.
* 常见的IO复用实现
1. select (Linux/Windows/BSD)
2. epoll (Linux)
3. kqueue (BSD/Mac OS X)

***

此文参考于 [hit-alibaba.github.io][hit-alibaba.github.io],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[hit-alibaba.github.io]: https://hit-alibaba.github.io/interview/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[arch]: /2017/02/basics-about-arch/ 'arch'
[interrupt_and_syscall]: /2017/02/basics-about-interrupt-and-syscall/ 'interrupt_and_syscall'
[concurrency]: /2017/02/basics-about-concurrency/ 'concurrency'
[memory_management]: /2017/02/basics-about-memory-management/ 'memory_management'
[disk_and_file]: /2017/02/basics-about-disk-and-file/ 'disk_and_file'