---
layout: post
title: 操作系统基础(五) -- 磁盘与文件
date: 2017-02-08 16:29:00 +08:00
tags: 温故而知新
---

***

#### 操作系统基础:

[操作系统基础(一) -- 数据单元][arch]  
[操作系统基础(二) -- 中断和系统调用][interrupt_and_syscall]  
[操作系统基础(三) -- 并发技术][concurrency]  
[操作系统基础(四) -- 内存管理基础][memory_management]  
[操作系统基础(五) -- 磁盘与文件][disk_and_file]  

***

### 磁盘调度

* 磁盘访问延迟 = 队列时间 + 控制器时间 + 寻道时间 + 旋转时间 + 传输时间
* 磁盘调度的目的是减小延迟,其中前两项可以忽略,寻道时间是主要矛盾.

#### 磁盘调度算法

* FCFS
1. 先进先出的调度策略
2. 这个策略具有公平的优点,因为每个请求都会得到处理,并且是按照接收到的顺序进行处理.

* SSTF (Shortest-seek-time First, 最短寻道时间优先)
1. 选择使磁头从当前位置开始移动最少的磁盘I/O请求,所以 SSTF 总是选择导致最小寻道时间的请求.
2. 总是选择最小寻找时间并不能保证平均寻找时间最小,但是能提供比 FCFS 算法更好的性能,会存在饥饿现象.

* SCAN
1. SSTF+中途不回折,每个请求都有处理机会.
2. SCAN 要求磁头仅仅沿一个方向移动,并在途中满足所有未完成的请求,直到它到达这个方向上的最后一个磁道,或者在这个方向上没有其他请求为止.
3. 由于磁头移动规律与电梯运行相似,SCAN 也被称为电梯算法.
4. SCAN 算法对最近扫描过的区域不公平,因此,它在访问局部性方面不如 FCFS 算法和 SSTF 算法好.

* C-SCAN
1. SCAN+直接移到另一端,两端请求都能很快处理.
2. 把扫描限定在一个方向,当访问到某个方向的最后一个磁道时,磁道返回磁盘相反方向磁道的末端,并再次开始扫描.
3. 其中“C”是Circular(环)的意思.

* LOOK 和 C-LOOK
1. 釆用SCAN算法和C-SCAN算法时磁头总是严格地遵循从盘面的一端到另一端.显然,在实际使用时还可以改进,即磁头移动只需要到达最远端的一个请求即可返回,不需要到达磁盘端点.
2. 这种形式的SCAN算法和C-SCAN算法称为LOOK和C-LOOK调度.
3. 这是因为它们在朝一个给定方向移动前会查看是否有请求.

***

### 文件系统

#### 分区表

* MBR: 支持最大卷为 2TB(Terabytes) 并且每个磁盘最多有4个主分区(或3个主分区,1个扩展分区和无限制的逻辑驱动器).
* GPT: 支持最大卷为 18EB(Exabytes) 并且每磁盘的分区数没有上限,只受到操作系统限制(由于分区表本身需要占用一定空间,最初规划硬盘分区时,留给分区表的空间决定了最多可以有多少个分区,IA-64版Windows限制最多有128个分区,这也是EFI标准规定的分区表的最小尺寸).另外,GPT分区磁盘有备份分区表来提高分区数据结构的完整性.

#### RAID 技术

* 磁盘阵列(Redundant Arrays of Independent Disks, RAID).独立冗余磁盘阵列.
* 原理是利用数组方式来作磁盘组,配合数据分散排列的设计,提升数据的安全性.

* RAID 0:
1. RAID 0是最早出现的RAID模式,需要2块以上的硬盘,可以提高整个磁盘的性能和吞吐量.
2. RAID 0没有提供冗余或错误修复能力,其中一块硬盘损坏,所有数据将遗失.
* RAID 1
1. RAID 1就是镜像,其原理为在主硬盘上存放数据的同时也在镜像硬盘上写一样的数据.
2. 当主硬盘(物理)损坏时,镜像硬盘则代替主硬盘的工作.因为有镜像硬盘做数据备份,所以RAID 1的数据安全性在所有的RAID级别上来说是最好的.
3. 但无论用多少磁盘做RAID 1,仅算一个磁盘的容量,是所有RAID中磁盘利用率最低的.

* RAID 2
1. 这是RAID 0的改良版,以汉明码(Hamming Code)的方式将数据进行编码后分区为独立的比特,并将数据分别写入硬盘中.
2. 因为在数据中加入了错误修正码(ECC, Error Correction Code),所以数据整体的容量会比原始数据大一些,RAID2最少要三台磁盘驱动器方能运作.

* RAID 3
1. 采用Bit－interleaving(数据交错存储)技术,它需要通过编码再将数据比特分割后分别存在硬盘中,而将同比特检查后单独存在一个硬盘中,但由于数据内的比特分散在不同的硬盘上,因此就算要读取一小段数据资料都可能需要所有的硬盘进行工作,所以这种规格比较适于读取大量数据时使用.

* RAID 4
1. 它与RAID 3不同的是它在分区时是以区块为单位分别存在硬盘中,但每次的数据访问都必须从同比特检查的那个硬盘中取出对应的同比特数据进行核对,由于过于频繁的使用,所以对硬盘的损耗可能会提高.(块交织技术, Block interleaving)  

* **RAID 2/3/4 在实际应用中很少使用**.
* RAID 5
1. RAID Level 5是一种储存性能,数据安全和存储成本兼顾的存储解决方案.它使用的是Disk Striping(硬盘分区)技术.
2. RAID 5至少需要三块硬盘,RAID 5不是对存储的数据进行备份,而是把数据和相对应的奇偶校验信息存储到组成RAID5的各个磁盘上,并且奇偶校验信息和相对应的数据分别存储于不同的磁盘上.
3. RAID 5 允许一块硬盘损坏.
4. 实际容量 Size = (N-1) * min(S1, S2, S3 ... SN)

* RAID 6
1. 与RAID 5相比,RAID 6增加第二个独立的奇偶校验信息块.两个独立的奇偶系统使用不同的算法,数据的可靠性非常高,即使两块磁盘同时失效也不会影响数据的使用.
2. RAID 6 至少需要4块硬盘.
3. 实际容量 Size = (N-2) * min(S1, S2, S3 ... SN)

* RAID 10/01 (RAID 1+0，RAID 0+1)
1. RAID 10是先镜射再分区数据,再将所有硬盘分为两组,视为是RAID 0的最低组合,然后将这两组各自视为RAID 1运作.
2. RAID 01则是跟RAID 10的程序相反,是先分区再将数据镜射到两组硬盘.它将所有的硬盘分为两组,变成RAID 1的最低组合,而将两组硬盘各自视为RAID 0运作.
3. 当RAID 10有一个硬盘受损,其余硬盘会继续运作.RAID 01只要有一个硬盘受损,同组RAID 0的所有硬盘都会停止运作,只剩下其他组的硬盘运作,可靠性较低.如果以六个硬盘建RAID 01,镜射再用三个建RAID 0,那么坏一个硬盘便会有三个硬盘脱机.因此,RAID 10远较RAID 01常用,零售主板绝大部份支持RAID 0/1/5/10,但不支持RAID 01.
4. RAID 10 至少需要4块硬盘,且硬盘数量必须为偶数.

#### 常见文件系统

* Windows: FAT, FAT16, FAT32, NTFS
* Linux: ext2/3/4, btrfs, ZFS
* macOS: HFS+

#### Linux文件权限

* Linux文件采用10个标志位来表示文件权限,如下所示:

```plain
-rw-r--r--  1 skyline  staff    20B  1 27 10:34 1.txt
drwxr-xr-x   5 skyline  staff   170B 12 23 19:01 ABTableViewCell
```

* 第一个字符一般用来区分文件和目录,其中:
1. d: 表示是一个目录,事实上在ext2fs中,目录是一个特殊的文件.
2. －: 表示这是一个普通的文件.
3. l: 表示这是一个符号链接文件,实际上它指向另一个文件.
4. b,c: 分别表示区块设备和其他的外围设备,是特殊类型的文件.
5. s,p: 这些文件关系到系统的数据结构和管道,通常很少见到.
* 第2～10个字符当中的每3个为一组,左边三个字符表示所有者权限,中间3个字符表示与所有者同一组的用户的权限,右边3个字符是其他用户的权限.
* 这三个一组共9个字符,代表的意义如下:
1. r(Read,读取): 对文件而言,具有读取文件内容的权限; 对目录来说,具有浏览目录的权限.
2. w(Write,写入): 对文件而言,具有新增,修改文件内容的权限; 对目录来说,具有删除,移动目录内文件的权限.
3. x(Execute,执行): 对文件而言,具有执行文件的权限; 对目录来说该用户具有进入目录的权限.
* 权限的掩码可以使用十进制数字表示:
1. 如果可读,权限是二进制的100,十进制是4;
2. 如果可写,权限是二进制的010,十进制是2;
3. 如果可运行,权限是二进制的001,十进制是1;
4. 具备多个权限,就把相应的 4,2,1 相加就可以了:若要rwx, 则4+2+1=7, 若要rw-, 则4+2=6, 若要r-x, 则4+1=5, 若要r--, 则=4, 若要-wx, 则2+1=3, 若要-w-, 则=2, 若要--x, 则=1, 若要---, 则=0.

* 默认的权限可用umask命令修改,用法非常简单,只需执行umask 777命令,便代表屏蔽所有的权限,因而之后建立的文件或目录,其权限都变成000,依次类推.
* 通常root帐号搭配umask命令的数值为022, 027和077,普通用户则是采用002,这样所产生的权限依次为755, 750, 700, 775.

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