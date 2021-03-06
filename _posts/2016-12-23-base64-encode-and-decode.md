---
layout: post
title: Base64 编码规则
date: 2016-12-23 15:57:24 +08:00
author: Srefan
catalog: true
tags:
    - 入门
    - 协议
    - Base64
---

### Base64 编码表

|     |  0  |  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |  9  |  A  |  B  |  C  |  D  |  E  |  F  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|  0  |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |  M  |  N  |  O  |  P  |
|  1  |  Q  |  R  |  S  |  T  |  U  |  V  |  W  |  X  |  Y  |  Z  |  a  |  b  |  c  |  d  |  e  |  f  |
|  2  |  g  |  h  |  i  |  j  |  k  |  l  |  m  |  n  |  o  |  p  |  q  |  r  |  s  |  t  |  u  |  v  |
|  3  |  w  |  x  |  y  |  z  |  0  |  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |  9  |  +  |  /  |


### Base64 编码规则

**Base64**, 就是选出64个字符----大写字母`A-Z`、小写字母`a-z`、数字`0-9`、符号`+`、`/`（再加上作为垫字的`=`，实际上是65个字符）----作为一个基本字符集. 将其他所有符号都转换成这个字符集中的字符.

具体规则如下:
> 第一步, 将每三个字节作为一组, 一共是24个二进制位. <br/>
> 第二步, 将这24个二进制位分为四组, 每个组有6个二进制位. <br/>
> 第三步, 在每组前面加两个00, 扩展成32个二进制位, 即四个字节. <br/>
> 第四步, 根据 Base64 编码表, 得到扩展后的每个字节的对应符号, 这就是Base64的编码值. <br/>
> 如果编码数据不是`3`的倍数, Base64用`\x00`字节在末尾补足缺少的位数, 再在编码的末尾加上1个或2个`=`号, (缺少几个字符补几个`=`，解码时, `=`会自动去掉).

Ps: Base64将三个字节转化成四个字节, 因此Base64编码后的文本, 会比原文本大出三分之一左右.

### Base64 编码实例

![Base64 Man Demo][img_1]

```markdown
第一步, "M"、"a"、"n" 的ASCII值分别是77、97、110, 对应的二进制值是 01001101、01100001、01101110.
       将它们连成一个24位的二进制字符串 010011010110000101101110.
       
第二步, 将这个24位的二进制字符串分成4组, 每组6个二进制位：010011、010110、000101、101110.

第三步, 在每组前面加两个00, 扩展成32个二进制位, 即四个字节：00010011、00010110、00000101、00101110.
       它们的十进制值分别是 19、22、5、46.
       
第四步, 根据Base64编码表, 得到每个值对应Base64编码, 即T、W、F、u.
```
所以, `Man`的 _Base64_ 编码就是`TWFu`.

#### 如果字节数不足三, 则这样处理:

##### a）二个字节的情况：  

* 将这二个字节的一共16个二进制位, 按照上面的规则, 转成三组, 最后一组除了前面加两个0以外, 后面也要加两个0.  
* 这样得到一个三位的Base64编码, 再在末尾补上一个"="号.  
      
* 比如, "Ma"这个字符串是两个字节, 可以转化成三组 00010011、00010110、00010000.  
* 对应Base64值分别为T、W、E, 再补上一个"="号, 因此"Ma"的Base64编码就是TWE=.  

所以, `Ma`的 _Base64_ 编码就是`TWE=`.

##### b）一个字节的情况：
* 将这一个字节的8个二进制位, 按照上面的规则转成二组, 最后一组除了前面加二个0以外, 后面再加4个0.
* 这样得到一个二位的Base64编码, 再在末尾补上两个"="号.
    
* 比如, "M"这个字母是一个字节, 可以转化为二组 00010011、00010000. 
* 对应的Base64值分别为T、Q, 再补上二个"="号, 因此"M"的Base64编码就是TQ==.

所以, `M`的 _Base64_ 编码就是`TQ==`.


[img_1]: /assets/images/base64/base64_man.png 'man'