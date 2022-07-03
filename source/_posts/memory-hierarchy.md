---
title: 存储器结构层次
date: 2022-01-24 15:48:50
tags: [操作系统, 存储器]
description: 你知道吗，位于CPU中的各类寄存器、CPU cache、内存 SSD硬盘、机械硬盘都是冯诺依曼模型中的存储单元，为什么设计了这么多存储器呢，他们之间的关系又是什么，存储器是怎么存储数据的，又是怎么提供给CPU使用的呢？
---

## 存储器层次结构

存储器通常分成以下几个级别:
- 寄存器
- CPU Cache
- 内存
- SSD/HDD硬盘

<img src="https://lucidreamiss-blog-1310999690.cos.ap-chengdu.myqcloud.com/memory-hierarchy/%E5%AD%98%E5%82%A8%E5%99%A8%E7%BB%93%E6%9E%84%E5%B1%82%E6%AC%A1.jpg" width=200/>

### 寄存器

最靠近CPU的控制单元与逻辑运算单元的就是寄存器了，他使用的材料速度是最快的，价格也是最昂贵的，存储容量很少。存储器的数量通常在几十到几百不等，每个寄存器可以用来存储一定字节的数据，比如

- 32位CPU中大多数寄存器可以存储**4**个字节
- 64位CPU中大多数可以存储**8**个字节

寄存器的访问速度非常快，一般要求在半个CPU时钟周期内完成读写，CPU时钟周期跟CPU主频息息相关，比如2GHz主频的CPU，那么它的时钟周期就是1/2G，也就是0.5ns。
CPU处理一条指令的时候，除了读写寄存器，还需要解码指令、控制指令和计算。如果寄存器的速度太慢，则会拉长指令的处理周期，从而给用户感觉很卡的样子。

{% note info %}
**时钟周期**：CPU的硬件参数都会有**GHz**这个参数，比如一个1GHz的CPU，指的是时钟频率为1G，代表着1秒会产生1G次数的脉冲信号，每一次脉冲信号高低电平的转换就是一个周期，称为时钟周期。
对于CPU来说，一个时钟周期CPU仅能完成一个最基本的动作，时钟频率越高，则时钟周期越短，那么CPU工作的速度也就越快。
{% endnote %}

### CPU Cache

CPU Cache 使用**SRAM（静态随机存储器）**芯片。
{%note info %}
SRAM(静态随机存储器)：一个bit数据，通常需要6根晶体管保存，SRAM存储密度不高，但访问速度很快，之所以称为静态存储器，是因为只要有电，数据就会一直保持存在，停电消失。
{% endnote %}

CPU Cache分为L1、L2、L3、这样的三层高速缓存。

![CPU三级高速缓存](https://lucidreamiss-blog-1310999690.cos.ap-chengdu.myqcloud.com/memory-hierarchy/cpu_cache.jpg)

#### L1高速缓存

L1高速缓存的访问速度几乎和寄存器一样快，通常只需要`2-4`个时钟周期，而大小在几十KB到几百KB不等。
每个CPU核心都有一块属于自己的L1高速缓存，指令和数据在L1是分开存放的，所以L1高速缓存通常分成指令缓存与数据缓存。

在Linux系统中，可以查看CPU L1 Cache **数据**缓存的容量大小：

```shell linux
$ cat /sys/devices/system/cpu/cpu0/cache/index0/size
32K
```

L1 Cache **指令**缓存的容量大小：

```shell linux
$ cat /sys/devices/system/cpu/cpu0/cache/index1/size
32K
```

#### L2高速缓存

L2高速缓存同样每个CPU核心都有，但是L2高速缓存位置离CPU更远，大小比L1更大，访问速度更慢，速度在10~20个时钟周期。

```shell linux
$ cat /sys/devices/system/cpu/cpu0/cache/index2/size
256K
```

#### L3高速缓存

速度更慢，20~60个时钟周期；容量更大，几MB到几十MB。
L3高速缓存通常多个CPU核心共用，*这里会存在一些并发问题，后续我们来看看CPU是如何解决该问题的*

```shell linux
$ cat /sys/devices/system/cpu/cpu0/cache/index3/size 
3072K
```

### 内存

内存使用**DRAM（动态随机存取存储器）**保存数据。
相比SRAM，DRAM的密度更高，功耗更低，有更大的容量，而且造价比SRAM芯片便宜很多。
DRAM存储一个bit的数据只需要一个电容和一个晶体管即可，但是因为数据会被存储在电容里，而电容会不断漏电，所以需要定时刷新电容，才能保证数据不会被丢失，这就是DRAM之所以被称为**动态**存储器的原因，只有不断刷新，数据才会被存储起来。
DRAM的访问电路与刷新电路都更复杂，所以访问的速度会更慢，内存速度大概在*200~300*个时钟周期。

### SSD/HDD硬盘

SSD即固态硬盘，结构与内存类似，优点是断电数据也存在，内存的读写速度大概比SSD快10~1000倍。

HDD即传统的机械硬盘，使用物理读写的方式，所以速度很慢，大概比内存慢10w倍左右，差不多已经被SSD代替了。


## 存储器的层次关系

现代的一台计算机，都用上了CPU Cache、内存、SSD/HDD硬盘这些存储设备了。

![存储器的层次关系](https://lucidreamiss-blog-1310999690.cos.ap-chengdu.myqcloud.com/memory-hierarchy/%E5%AD%98%E5%82%A8%E5%99%A8%E7%9A%84%E5%B1%82%E6%AC%A1%E5%85%B3%E7%B3%BB%E5%9B%BE.webp)

每个存储器只和与他相邻的一层存储器设备打交道。