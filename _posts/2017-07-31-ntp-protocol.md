---
layout: post
title: NTP 协议简单分析
date: 2017-07-31 14:33:00 +08:00
tags: 协议控
---

***

### 一.概述

* **NTP** (Network Time Protocol, 网络时间协议) 是由 [**RFC 5905**][Link_2] 定义的时间同步协议, 用来在分布式时间服务器和客户端之间进行时间同步, 是一个跨越广域网或局域网的复杂的同步时间协议, 它通常可获得毫秒级的精度.
* **NTP** 基于 **UDP** 报文进行传输, 使用的UDP端口号为 **123**.
* 使用 NTP 的目的是对网络内所有具有时钟的设备进行时钟同步, 使网络内所有设备的时钟保持一致, 从而使设备能够提供基于统一时间的多种应用.
* 对于运行 NTP 的本地系统, 既可以接收来自其他时钟源的同步, 又可以作为时钟源同步其他的时钟, 并且可以和其他设备相互同步.

### 二.工作原理

#### 实现方式

* 无线时钟: 服务器系统可以通过串口连接一个无线时钟. 无线时钟接收 GPS 的卫星发射的信号来决定当前时间. 无线时钟是一个非常精确的时间源, 但是需要花一定的费用.
* 时间服务器: 还可以使用网络中 NTP 时间服务器, 通过这个服务器来同步网络中的系统的时钟.
* 局域网内的同步: 如果只是需要在本局域网内进行系统间的时钟同步, 那么就可以使用局域网中任何一个系统的时钟. 你需要选择局域网中的一个节点的时钟作"权威的"的时间源, 然后其它的节点就只需要与这个时间源进行时间同步即可. 使用这种方式, 所有的节点都会使用一个公共的系统时钟, 但是不需要和局域网外的系统进行时钟同步. 如果一个系统在一个局域网的内部, 同时又不能使用无线时钟, 这种方式是最好的选择.

#### 工作流程

* Device A 和 Device B 通过网络相连, 有自己独立的系统时钟, 通过 NTP 实现各自系统时钟的自动同步.

> Device A 和 Device B 的系统时钟同步之前, Device A 的时钟设定为 10:00:00 AM, Device B 的时钟设定为 11:00:00 AM.  
> Device B 作为 NTP 时间服务器, 即 Device A 使自己的时钟与 Device B 时钟同步.  
> NTP 报文在 Device A 和 Device B 之间单向传输所需要的时间为 1s.

![img_2][img_2]

1. Device A 发送一个NTP报文给 Device B, 该报文带有它离开 Device A 时的时间戳, 该时间戳为 10:00:00 AM (T1).
2. 当此 NTP 报文到达 Device B 时, Device B 加上自己的时间戳, 该时间戳为 11:00:01 AM (T2).
3. 当此 NTP 报文离开 Device B 时, Device B 加上自己的时间戳, 该时间戳为 11:00:02 AM (T3).
4. 当 Device A 接收到该响应报文时, Device A 的本地时间为 10:00:03 AM (T4).

* 至此, Device A 已经拥有足够的信息来计算两个重要的参数:
* NTP 报文的往返时延 Delay = (T4-T1) - (T3-T2) = 2s.
* Device A 相对 Device B 的时间差 Offset = ((T2-T1) + (T3-T4))/2 = 1h.
* 这样, Device A 就能够根据这些信息来设定自己的时钟, 使之与 Device B 的时钟同步.

* 设备可以采用多种 NTP 工作模式进行时间同步:
1. 客户端/服务器模式
2. 对称模式
3. 广播模式
4. 组播模式

#### 客户端/服务器模式

* 在客户端/服务器模式中, 客户端向服务器发送时钟同步报文, 报文中的 Mode 字段设置为3 (客户模式). 服务器端收到报文后会自动工作在服务器模式, 并发送应答报文, 报文中的 Mode 字段设置为4 (服务器模式). 客户端收到应答报文后, 进行时钟过滤和选择, 并同步到优选的服务器.
* 在该模式下, 客户端能同步到服务器, 而服务器无法同步到客户端.

![img_3][img_3]

#### 对称模式

* 在对称模式中, 主动对称体和被动对称体之间首先交互 Mode 字段为 3 (客户端模式) 和 4 (服务器模式) 的NTP报文. 之后, 主动对称体向被动对称体发送时钟同步报文, 报文中的 Mode 字段设置为 1 (主动对称体), 被动对等体收到报文后自动工作在被动对等体模式, 并发送应答报文, 报文中的Mode字段设置为 2 (被动对等体). 经过报文的交互, 对等体模式建立起来. 主动对等体和被动对等体可以互相同步. 如果双方的时钟都已经同步, 则以层数小的时钟为准.

![img_4][img_4]

#### 广播模式

* 在广播模式中, 服务器端周期性地向广播地址 `255.255.255.255` 发送时钟同步报文, 报文中的 Mode 字段设置为5 (广播模式). 客户端侦听来自服务器的广播报文. 当客户端接收到第一个广播报文后, 客户端与服务器交互 Mode 字段为3 (客户模式) 和 4 (服务器模式) 的NTP报文, 以获得客户端与服务器间的网络延迟. 之后, 客户端就进入广播客户端模式, 继续侦听广播报文的到来, 根据到来的广播报文对系统时钟进行同步.

![img_5][img_5]

#### 组播模式

* 在组播模式中, 服务器端周期性地向用户配置的组播地址 (若用户没有配置组播地址, 则使用默认的 NTP 组播地址224.0.1.1) 发送时钟同步报文, 报文中的 Mode 字段设置为 5 (组播模式). 客户端侦听来自服务器的组播报文. 当客户端接收到第一个组播报文后, 客户端与服务器交互 Mode 字段为 3 (客户模式) 和 4 (服务器模式) 的 NTP 报文, 以获得客户端与服务器间的网络延迟. 之后, 客户端就进入组播客户模式, 继续侦听组播报文的到来, 根据到来的组播报文对系统时钟进行同步.

![img_6][img_6]

### 三.NTP报文格式

* **NTP** 有两种不同类型的报文, 一种是 **时钟同步报文**, 一种是 **控制报文**.
* **控制报文** 仅用于需要网络管理的场合, 对于时钟同步功能不是必需的, 暂不做分析.
* 时钟同步报文格式如下:

![img_1][img_1]

* LI (Leap Indicator): 闰秒标识器, 长度为 2 Bits, 用来预警最近一分钟插入 1s 或者删除 1s.

| LI | Value | 含义 |
|:----:|:----:|:----:|
| 00 | 0 | 无预告 |
| 01 | 1 | 醉经一分钟有 61s |
| 10 | 2 | 最近一分钟有 59s |
| 11 | 3 | 警告状态(时钟未同步) |

* VN (Version Number): 版本号, 长度为 3 Bits, 目前最新的版本是 4, 向下兼容指定于 [**RFC 1305**][Link_3] 的版本 3.
* Mode: 工作模式, 长度为 3 Bits.
> 点对点模式下, 客户端请求时设置此字段为 3, 服务器应答时设置此字段为 4.  
> 广播模式下, 服务器应答设置此字段为 5.

| Mode | Value | 含义 |
|:----:|:----:|:----:|
| 000 | 0 | 保留 |
| 001 | 1 | 主动对称模式 |
| 010 | 2 | 被动对称模式 |
| 011 | 3 | 客户端模式 |
| 100 | 4 | 服务器模式 |
| 101 | 5 | 广播或组播模式 |
| 110 | 6 | NTP控制报文 |
| 111 | 7 | 预留给内部使用 |

* Stratum: 系统时钟的层数, 长度为 8 Bits, 取值范围 1~16, 定义时钟的准确度. 层数为 1 的时钟准确度最高, 准确度从 1 到 16 依次递减, 阶层的上限为15, 层数为 16的时钟处于**未同步**状态, 不能作为参考时钟.
* NTP 获得 UTC 的时间来源可以是原子钟, 天文台, 卫星, 也可以从Internet上获取.
* stratum-0 是高精度计时设备, 例如原子钟 (如铯, 铷), GPS时钟或其他无线电时钟. 它们生成非常精确的脉冲秒信号, 触发所连接计算机上的中断和时间戳. 也称为参考 (基准) 时钟.
* stratum-1 是与 stratum-0 设备相连, 在几微秒误差内同步系统时钟的计算机.
* 时间是按 NTP 服务器的等级传播. 按照距离外部 UTC 源的远近将所有服务器归入不同的 Stratum (层) 中. Stratum-1 在顶层, 有外部 UTC 接入, 而Stratum-2 则从 Stratum-1 获取时间, Stratum-3 从 Stratum-2 获取时间, 以此类推, 但 Stratum 层的总数限制在15以内. 所有这些服务器在逻辑上形成阶梯式的架构并相互连接, 而 Stratum-1 的时间服务器是整个系统的基础.

| stratum | 含义 |
|:----:|:----:|
| 0 | 未指定或者难以获得 |
| 1 | 主要参考(如: 无线电时钟,校正的原子时钟) |
| 2~15 | 第二参考(Via NTP) |
| 16 | 未同步状态, 不能作为参考时钟 |

* Poll: 轮询间隔时间, 长度为 8 Bits, 两个连续NTP报文之间的时间间隔, 用 2 的幂来表示, 比如值为 6 表示最小间隔为 2^6 = 64s.
* Precision: 系统时钟的精度, 长度为 8 Bits, 用 2 的幂来表示, 比如 50Hz(20ms)或者60Hz(16.67ms) 可以表示成值 -5 (2^-5 = 0.03125s = 31.25ms).
* Root Delay: 本地到主参考时钟源的往返时间, 长度为 32 Bits, 有 15～16 位小数部分的无符号定点小数.
* Root Dispersion: 系统时钟相对于主参考时钟的最大误差, 长度为 32 Bits, 有 15～16 位小数部分的无符号定点小数.

> 网络对称性:  
> 通过两次测量来估计链路延迟一般估算方法是假设链路是对称的, 即时间服务器到客户端的延迟等于客户端到时间服务器的延迟.  
> 这种假设是理想化的, 实际的无线链路往往受到各种因素影响,  
> 例如 多径, 时变 而不完全对称.  
> 网络的拓扑结构:  
> 简单的点对点拓扑结构能达到较高的同步精度, 而一些复杂的网络容易受到网络延迟抖动的影响, 且精度与网络负载情况相关.

* Reference Identifier: 参考时钟源的标识, 长度为 32 Bits.
* Reference Timestamp: 系统时钟最后一次被设定或更新的时间, 长度为 64 Bits, 无符号定点数, 前 32 Bits 表示整数部分, 后 32 Bits 表示小数部分, 理论分辨率 2^−32s.

> 时间戳的记录以秒的形式从 1900-01-01 00:00:00 算起.  
> NTP的时间精度在 WAN 为 数十毫秒,  
> 在 LAN 为 亚毫秒级甚至更高,  
> 在 Internet 上绝大部分能提供 1-50ms 的精确度, 取决于同步源和网络路径等特性.  
> 比如: 当前时间为 1902-01-01 01:01:01, 与 1900 的参考时间相差为:  
> (365*2*24*60*60+3600+60+1) = 63075661s = 0x03C2754D s.  
> 转换成二进制: 0000 0011 1100 0010 0111 0101 0100 1101 XXXX XXXX XXXX XXXX XXXX XXXX XXXX XXXX.  
> 因为只有 32 Bits 表示秒数, 所以到了 2036 年数据就会溢出.  
> 所以以 136 年为一个周期置 0 , 会用一些外部的方法来表示是相对于 1900 年还是 2036 年的时间.  
> NTP 的未来版本可能将时间表示扩展到 128 Bits: 其中 64 Bits 表示秒, 64 Bits 表示秒的小数. 当前的 NTPv4 格式支持 "时代数字" (Era Number)和 "时代偏移" (Era Offset), 正确使用它们应该有助于解决日期翻转问题. 据Mills称: "64 Bits 秒的小数足以分辨光子以光速通过电子所需的时间. 64 Bits 的秒足以提供明确的时间表示, 直到宇宙变暗."

* Originate Timestamp: NTP请求报文离开发送端时发送端的本地时间, 长度为 64 Bits.
* Receive Timestamp: NTP请求报文到达接收端时接收端的本地时间, 长度为 64 Bits.
* Transmit Timestamp: 应答报文离开应答者时应答者的本地时间, 长度为 64 Bits.
* Authenticator(Optional): 验证信息, 长度为 96 Bits, (可选信息), 当实现了 NTP 认证模式时, 主要标识符和信息数字域就包括已定义的信息认证代码 (MAC) 信息.

> 识别机制抗干扰和恶意破坏:  
> 为防止对时间服务器的恶意破坏, NTP使用了识别 (Authentication) 机制.  
> 检查来对时的信息是否是真正来自所宣称的服务器并检查资料的返回路径, 以提供对抗干扰的保护机制.


### 四.实例分析

* 时间服务器接收同步消息,应答反馈的数据如下:
* `1c0104ec 00000000 00000048 47505373 dd26aa9f f7e47f4e dd26aaa7 4f5022d9 dd26aaa7 5f6f1524 dd26aaa7 5f716a6a`
* 长度为 48 Bytes.

#### wireshark 抓包如下:

![img_7][img_7]

#### 解析数据

* `1c0104ec` 转换成二进制: `0001 1100 0000 0001 0000 0100 1110 1100`
1. 00.. .... = Leap Indicator: no warning (0)
2. ..01 1... = Version Number: NTP Version 3 (3)
3. .... .100 = Mode: Server (4)
4. 0000 0001 = Peer Clock Stratum: primary reference (1)
5. 0000 0100 = Peer Polling Interval: 4 (2^4 = 16s)
6. 1110 1100 = Peer Clock Precision: -20 (2^-20 = 0.000001s). ps: (补码: `1110 1100`, 转换为反码: `1110 1011`, 转换为原码: `0001 0100` = `20`. 添加符号为 `-20`, 精度为 `2^-20 = 0.000001s`.)

* `00000000` 秒数为 `0000`, 秒的小数为 `0000`. 所以: Root Delay: 0.0000s
* `00000048` 秒数为 `0000`, 秒的小数为 `0048`, 转换二进制: `0000 0000 0100 1000`. Root Dispersion: (0 + 2^-10 + 2^-13 = 0.0011s).
* `47505373` 采用ASCII码, Reference ID: Unidentified reference source 'GPSs'
* `dd26aa9f f7e47f4e` 秒数为 `dd26aa9f`, 转换成 Unix 时间戳 (1900年 转换为 1970年开始): `0xdd26aa9f` - `0x83aa7e80` = `0x597c2c1f` = `1501309983` 即 `2017-07-29 14:33:03`. 秒的小数为 `f7e47f4e`, 转换成二进制: `1111 0111 1110 0100 0111 1111 0100 1110` = `2^-1 +  2^-2 + 2^-3 + 2^-4 + 2^-6 + 2^-7 + 2^-8 + 2^-9 + 2^-10 + 2^-11 + 2^-14 + 2^-18 + 2^-19 + 2^-20 + 2^-21 + 2^-22 + 2^-23 + 2^-24 + 2^-26 + 2^-29 + 2^-30 + 2^-31 = 0.968330000`. Reference Timestamp: 2017-07-29 14:33:03.068330000s UTC +08:00.
* `dd26aaa7 4f5022d9` 秒数为 `dd26aaa7` = `2017-07-29 14:33:11`. 秒的小数为 `4f5022d9` = `0.309816000`. Reference Timestamp: 2017-07-29 14:33:11.309816000s UTC +08:00.
* `dd26aaa7 5f6f1524` 秒数为 `dd26aaa7` = `2017-07-29 14:33:11`. 秒的小数为 `5f6f1524` = `0.372788000`. Reference Timestamp: 2017-07-29 14:33:11.372788000s UTC +08:00.
* `dd26aaa7 5f716a6a` 秒数为 `dd26aaa7` = `2017-07-29 14:33:11`. 秒的小数为 `5f716a6a` = `0.372824000`. Reference Timestamp: 2017-07-29 14:33:11.372824000s UTC +08:00.

### 五.参考链接

此文参考于 [Micyuao的博客][Link_1],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: http://blog.163.com/yzc_5001/blog/static/2061963420121283050787/
[Link_2]: https://tools.ietf.org/pdf/rfc5905.pdf
[Link_3]: https://tools.ietf.org/pdf/rfc1305.pdf

[img_1]: /assets/images/ntp/ntp_format.jpg "format"
[img_2]: /assets/images/ntp/process.jpg "process"
[img_3]: /assets/images/ntp/client_server.jpg "client_server"
[img_4]: /assets/images/ntp/peer.jpg "peer"
[img_5]: /assets/images/ntp/broadcast.jpg "broadcast"
[img_6]: /assets/images/ntp/mutlicast.jpg "mutlicast"
[img_7]: /assets/images/ntp/example.jpg "example"