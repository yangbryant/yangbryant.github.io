---
layout: post
title: VMware 虚拟机扩展 Linux 根目录磁盘空间
date: 2017-03-25 13:35:30 +08:00
tags: 终结者
---

## 问题根源

VMware虚拟机创建时,默认是20G磁盘空间,随着代码越来越多,发现20G是不够用的,会导致出现空间出现100%的情况.

```bash
# sudo df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 2.0G     0  2.0G   0% /dev
/dev/mapper/fedora-root   17G   17G  404K 100% /
tmpfs                    2.0G  628K  2.0G   1% /tmp
/dev/sda1                976M  114M  796M  13% /boot
```

所以我们需要扩展磁盘空间.

***

## 问题处理

### 1.虚拟机配置扩容

* 关闭虚拟机.
* 进入 虚拟机->设置->磁盘 界面.
* 磁盘空间从 20G -> 50G (根据个人需求).

![vmware][vmware]

### 2.查看分区

* 登录Linux系统, 打开终端工具.

* 查看文件系统, `df -h`. 发现文件系统未增加大小.

```bash
# sudo df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 2.0G     0  2.0G   0% /dev
tmpfs                    2.0G  272K  2.0G   1% /dev/shm
tmpfs                    2.0G  1.7M  2.0G   1% /run
tmpfs                    2.0G     0  2.0G   0% /sys/fs/cgroup
/dev/mapper/fedora-root   17G   17G  404K 100% /
tmpfs                    2.0G  144K  2.0G   1% /tmp
/dev/sda1                976M  114M  796M  13% /boot
tmpfs                    396M   28K  396M   1% /run/user/42
tmpfs                    396M  2.3M  393M   1% /run/user/1000
```

* 查看硬盘分区情况, `fdisk -l`. 发现硬盘空间确实已经扩展到了50G.

```bash
# sudo fdisk -l
Disk /dev/sda: 50 GiB, 53687091200 bytes, 104857600 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x53c8c32a

Device     Boot    Start       End  Sectors Size Id Type
/dev/sda1  *        2048   2099199  2097152   1G 83 Linux
/dev/sda2        2099200  41943039 39843840  19G 8e Linux LVM


Disk /dev/mapper/fedora-root: 17 GiB, 18199085056 bytes, 35545088 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/fedora-swap: 2 GiB, 2147483648 bytes, 4194304 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

### 3.创建分区

* 现在对硬盘 `/dev/sda` 进行硬盘操作. 执行 `fdisk /dev/sda`. 进入 `fdisk` 的命令模式.

```bash
# sudo fdisk /dev/sda
Welcome to fdisk (util-linux 2.28.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Command (m for help): 
```

* 使用命令 `n` 用于添加新分区, 然后选择 `p` primary 类型, 最后设置磁盘的分区位置.

```bash
Command (m for help): n
Partition type
   p   primary (2 primary, 0 extended, 1 free)
   e   extended (container for logical partitions)
Select (default e): p

Selected partition 3
First sector (41943040-104857599, default 41943040): 
Last sector, +sectors or +size{K,M,G,T,P} (41943040-104857599, default 104857599): 

Created a new partition 3 of type 'Linux' and of size 30 GiB.
```

* 使用命令 `w` 保存修改. 重启虚拟机.

```bash
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Re-reading the partition table failed.: Device or resource busy

The kernel still uses the old table. The new table will be used at the next reboot or after you run partprobe(8) or kpartx(8).
```

### 4.更改分区类型LVM

* 新建分区 `/dev/sda3` 是Linux, 不是 Linux LVM. 使用 `fdisk /dev/sda` 改成 LVM .

```bash
# sudo fdisk /dev/sda

Welcome to fdisk (util-linux 2.28.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Command (m for help): 
```

* 使用命令 `t` 用于改变分区系统, 然后选择 `3` 指定分区号, 最后选择 `8e` 改成指定的类型.

```bash
Command (m for help): t // 改变分区系统id
Partition number (1-3, default 3): 3 // 指定分区号
Partition type (type L to list all types): 8e // 指定要改成的id号,8e代表LVM

Changed type of partition 'Linux' to 'Linux LVM'.
```

* 使用命令 `w` 保存修改. 再次重启虚拟机, 否则无法扩充分区.

```bash
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Re-reading the partition table failed.: Device or resource busy

The kernel still uses the old table. The new table will be used at the next reboot or after you run partprobe(8) or kpartx(8).
```

### 5.格式化分区

* 重启系统后, `fdisk -l` 查看发现硬盘多了一块分区.

```bash
# sudo fdisk -l
Disk /dev/sda: 50 GiB, 53687091200 bytes, 104857600 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x53c8c32a

Device     Boot     Start       End  Sectors Size Id Type
/dev/sda1  *         2048   2099199  2097152   1G 83 Linux
/dev/sda2         2099200  41943039 39843840  19G 8e Linux LVM
/dev/sda3        41943040 104857599 62914560  30G 8e Linux

Disk /dev/mapper/fedora-root: 17 GiB, 18199085056 bytes, 35545088 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/fedora-swap: 2 GiB, 2147483648 bytes, 4194304 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

* 使用 `df -T` 查看磁盘分区类型. 发现 `/` 根目录为 `ext4` 格式.

```bash
# sudo df -T
Filesystem              Type     1K-blocks     Used Available Use% Mounted on
devtmpfs                devtmpfs   2011860        0   2011860   0% /dev
tmpfs                   tmpfs      2023476      264   2023212   1% /dev/shm
tmpfs                   tmpfs      2023476     1648   2021828   1% /run
tmpfs                   tmpfs      2023476        0   2023476   0% /sys/fs/cgroup
/dev/mapper/fedora-root ext4      17825792 17825388       404 100% /
tmpfs                   tmpfs      2023476      144   2023332   1% /tmp
/dev/sda1               ext4        999320   115876    814632  13% /boot
tmpfs                   tmpfs       404692       28    404664   1% /run/user/42
tmpfs                   tmpfs       404692     2352    402340   1% /run/user/1000
```

* 使用 `mkfs -t ext4 /dev/sda3` 创建文件格式.

```bash
# sudo mkfs -t ext4 /dev/sda3 
mke2fs 1.43.1 (08-Jun-2016)
Creating filesystem with 262144 4k blocks and 65536 inodes
Filesystem UUID: 64c8c4f2-955c-49e2-9200-8c897a0d1c6a
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done
```

### 6.扩充新分区

* 使用 `lvs` 查看卷组情况.

```bash
# sudo lvs
  LV   VG     Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root fedora -wi-ao---- 16.95g                                                    
  swap fedora -wi-ao----  2.00g 
```

* 使用 `pvcreate /dev/sda3` 将物理硬盘分区初始化为物理卷, 以便被LVM使用.

```bash
# sudo pvcreate /dev/sda3 
WARNING: ext4 signature detected on /dev/sda3 at offset 1080. Wipe it? [y/n]: y
  Wiping ext4 signature on /dev/sda4.
  Physical volume "/dev/sda4" successfully created.
  WARNING: D-Bus notification failed: The name com.redhat.lvmdbus1 was not provided by any .service files
```

* 使用 `vgextend fedora /dev/sda3` 向卷组中添加物理卷来增加卷组的容量.
* ps: 卷组的名字从 `df -h` 的命令中获得. `/dev/mapper/fedora-root` 中 `fedora` 即为名字.

```bash
# sudo vgextend fedora /dev/sda3
  Volume group "fedora" successfully extended
  WARNING: D-Bus notification failed: The name com.redhat.lvmdbus1 was not provided by any .service file
```

* 用 `vgdisplay` 查看LNM卷组的元数据信息.
* 主要查看 `Free PE / Size 7680 / 30.00GB`, 说明我们最多可以有30.00GB的扩充空间. 一般选择小于30.00GB.

```bash
# sudo vgdisplay 
  --- Volume group ---
  VG Name               fedora
  System ID             
  Format                lvm2
  Metadata Areas        3
  Metadata Sequence No  6
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                3
  Act PV                3
  VG Size               19.99 GiB
  PE Size               4.00 MiB
  Total PE              12531
  Alloc PE / Size       4851 / 18.95 GiB
  Free  PE / Size       7680 / 30.00 GiB
  VG UUID               dEVnw3-gwaT-zmWA-ZLmN-n0bX-cuUr-VTqYUA
```

* 使用 `lvextend -L+29.8G /dev/fedora/root /dev/sda3` 扩展空间.

```bash
# sudo lvextend -L+29.8G /dev/fedora/root /dev/sda3
  Rounding size to boundary between physical extents: 29.80 GiB.
  Size of logical volume fedora/root changed from 16.95 GiB (4835 extents) to 46.90 GiB (12163 extents).
  Logical volume fedora/root successfully resized.
  WARNING: D-Bus notification failed: The name com.redhat.lvmdbus1 was not provided by any .service files
```

* 使用 `e2fsck -a /dev/fedora/root` 检查文件系统错误.

```bash
# sudo e2fsck -a /dev/fedora/root
e2fsck 1.43.1 (08-Jun-2016)
Warning!  /dev/fedora/root is mounted.
Warning: skipping journal recovery because doing a read-only filesystem check.
/dev/fedora/root: clean, 943676/3145728 files, 7781825/12557312 blocks
```

* 使用 `resize2fs /dev/fedora/root` 来增加或收缩未加载的文件系统.

```bash
# sudo resize2fs /dev/fedora/root 
resize2fs 1.43.1 (08-Jun-2016)
Filesystem at /dev/fedora/root is mounted on /; on-line resizing required
old_desc_blocks = 6, new_desc_blocks = 6
The filesystem on /dev/fedora/root is now 12557312 (4k) blocks long.
```

* 使用 `df -h` 查看文件系统大小, 发现扩展分区成功了.

```bash
# sudo df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 2.0G     0  2.0G   0% /dev
tmpfs                    2.0G  340K  2.0G   1% /dev/shm
tmpfs                    2.0G  1.7M  2.0G   1% /run
tmpfs                    2.0G     0  2.0G   0% /sys/fs/cgroup
/dev/mapper/fedora-root   47G   17G   30G  39% /
tmpfs                    2.0G  144K  2.0G   1% /tmp
/dev/sda1                976M  114M  796M  13% /boot
tmpfs                    396M   28K  396M   1% /run/user/42
tmpfs                    396M  2.3M  393M   1% /run/user/1000
```

## 相关资料

此文参考于 [51CTO博客][wlzxzxw's Blog] 和 [CSDN博客][skyWalker_ONLY's Blog],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[vmware]: /assets/images/vmware/vmware.png 'vmware'

[wlzxzxw's Blog]: http://zhongxw.blog.51cto.com/3429597/787219/
[skyWalker_ONLY's Blog]: http://blog.csdn.net/skywalker_only/article/details/16340861/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/
