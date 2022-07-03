---
title: CPU Cache
date: 2022-01-25 19:50:59
tags: [操作系统, CPU, CPU Cache]
---

## CPU Cache的数据结构与读取过程

`CPU Cache`的数据是从内存中读取过来的，它是以一小块一小块读取数据的，而不是按照单个元素来读取数据的，在CPU Cache中，这样一小块一小块的数据，被称为`Cache Line` (缓存块)。Cache Line对应到内存的那一小块数据称为`Block`(内存块)。
可以使用以下命令查看`Cache Line`的大小:

```shell linux
$ cat /sys/devices/system/cpu/cpu0/cache/index0/coherency_line_size
64
```

64即64字节，即`L1 CPU Cache`一次载入的数据大小为64字节。
比如，有一个`int array[100]`的数组，当载入array[0]时，也会同时将array[1]~array[15]载入，作为一个Cache Line存入到CPU Cache中，那么当下次再访问这些元素时，就可以直接在内存中读取了，从而大大提高了CPU读取数据的性能。
事实上，CPU读取数据的时候，无论数据是否存放到Cache中，CPU都是先访问Cache，只有当Cache中找不到数据后，才会去访问内存，并把内存中的数据读入到Cache中，CPU再从Cache中获取数据，这样的交互方式也满足存储器的层次关系，即每层存储器只与他的上一层和下一层交互。

那么CPU是怎么知道要访问的内存数据缓存到了CPU Cache里面的呢？

### 直接映射Cache

我们先以直接映射为例，看看CPU Cache的数据结构和访问逻辑。
`直接映射Cache`采用的策略就是把`Block（内存块）`的地址始终映射在一个`Cache Line(缓存块)`的地址上，映射关系也简单粗暴的使用**取模运算**。
如下图，地址为**7**的`Cache Line（缓存块）`可以映射地址为**7、15、23、31**的`Block（内存块）`

![求模运算策略](https://lucidreamiss-blog-1310999690.cos.ap-chengdu.myqcloud.com/cpu-cache/%E6%B1%82%E6%A8%A1%E6%98%A0%E5%B0%84%E7%AD%96%E7%95%A5.webp)

我们也能发现，`Block`和`Cache Line`是多对一的，这里就需要特定的标识来区别不同的`Block`，这里特定的标识被称为`Tag(组标记)`。`Tag`会记录当前存储的数据对应哪个`Block`。除了`Tag`外，Cache Line中还有两个信息：

- `Data`，从内存中实际加载进来的数据。
- `Valid bit（有效位）`，用来标记对应的CPU Line中的数据是否有效，0表示该数据失效，此时CPU会重新访问内存来加载数据。

CPU在从CPU Cache读取数据的时候，仅会读取`Cache Line`中一部分数据，这部分数据片段称为`Word（字）`，怎么在`Cache Line`中获取`Word`呢，则需要一个offset（偏移量）。

因此，一个内存的访问地址，包括`Tag（组标记）、CPU Cache地址、Offset（偏移量）`这三种信息，靠这三个信息就可以取得CPU想要的数据。

那么CPU Cache的数据结构就包括`地址、Valid bit（有效位）、Tag（组标记）、Data（数据）`组成。

### 全相连Cache

### 组相连Cache

## CPU Cache的数据写入过程

上面我们聊了CPU是怎么从CPU Cache中读数据的，接下来我们聊下CPU是怎么通过CPU Cache写数据的。

我们知道，`CPU Cache`的职责是为`内存`缓冲数据以便供`CPU`使用的，当CPU想要向`内存`写入数据的时候，也必须经过CPU Cache这一层，再由CPU Cache把数据同步到内存里，那么同步时机是什么时候呢？下面有两种方式：

- 写直达，把数据直接同时写入到内存与Cache中。
  - 当CPU Cache中有数据时，更新CPU Cache的数据，再把CPU Cache的数据同步到内存中。
  - 当CPU Cache没有数据时，直接把数据写入到内存中。
- 写回，为减少数据同步内存的频率，就出现了写回机制：当发生写操作时，新的数据仅仅被写入Cache Block中，只有当修改过的Cache Block*被替换*时，才需要写到内存中，以此来减少写回频率。
  - 当CPU Cache中有数据时，将数据写到CPU Cache中，并将数据标记为脏。
  - 当CPU Cache中对应的Cache Line存放的是另一块Block数据时，如果该Cache Line被标记为脏，则需要将其写入到内存中，然后再将需要的数据从内存写入到CPU Cache中，再讲CPU返回的数据写到CPU Cache中，同时将其标记为脏。（为什么不直接把数据写入到内存里呢？而是间接使用Cache呢？实际上在单核CPU中，这么多是没有问题的，而对于多核CPU，就存在缓存一致性的问题了，这样做的目的就是为了保持缓存一致性。）

## 缓存一致性问题

在单核CPU的操作系统中，该问题不存在，现在计算机大多是多核CPU，每个CPU核心都有自己独立的CPU Cache，此时就会出现CPU的缓存一致性问题。
举个例子：
当两个CPU都缓存了`i = 0`的Block后，此时`A CPU`修改了i的值（写回策略，不会把值同步到内存中去），但此时`B CPU`中的还是1。

![缓存一致性](https://lucidreamiss-blog-1310999690.cos.ap-chengdu.myqcloud.com/cpu-cache/%E7%BC%93%E5%AD%98%E4%B8%80%E8%87%B4%E6%80%A7%E9%97%AE%E9%A2%98%E4%BE%8B%E5%AD%902.webp)

要解决缓存一致性的问题，就需要一种机制，来同步两个不同核心里面的缓存数据，我们来看下实现该机制需要具备哪些能力。

- `Write Propagation（写传播）`：某个CPU的数据更新是，必须要传播给其他核心的CPU
- `Transaction Serialization（事务的串行话）`：某个CPU核心里对数据的操作，他其他核心中，顺序是要一样的。

第一点写传播很容易理解，事务的串行化该怎么理解呢？假设有ABCD三个CPU，A离C近，B离D近，AC现在都要对i变量修改，A要将i改为100，B要将改为200。在传输速率一致的情况下，C先接收到A变更的信号，再接收B的，那么则看到的消息为i=100,i=200。D则与之相反，这里仔细发散去想会有很多问题。

所以我们要保证事务的串行化，保证该机制需要做到以下两点：

- CPU对于Cache中数据的操作，需要同步给其他CPU核心。
- 引入锁，如果两个CPU同时更新数据，只有拿到锁的CPU才可以更新数据。

### 总线嗅探

不多介绍，类似观察者模式，每个CPU都观察其他CPU的数据变更，这样可以做到`Write Propagation（写传播）`但无法做到`Transaction Serialization（事务的串行化）`。

### MESI协议（牛逼的来了）

MESI协议基于总线嗅探机制，利用状态机实现了事务串行化，同时还降低了总线嗅探时信息传播的频率。

MESI协议定义了Cache Line的四种状态，分别是`Modified（已修改）`、`Exclusive（独占）`、`Shared（共享）`、`Invalidated（失效）`。

- `Modified`：还记得前面提过的写回吗，当CPU对CPU Cache的数据修改后，会为其添加脏标记，这里的脏标记即`Modified`状态
- `Invalidated`：失效，这表示该CPU核心中的Cache Line已经失效了，不能直接使用。
- `Shared`：此时`Block`与`Cache Line`的数据一致，且多个CPU核心都有该`Block`的缓存。
- `Exclusive`：独占，此时`Block`与`Cache Line`的数据一致，且仅有一个CPU拥有该份数据。

![MESI状态转换过程](https://lucidreamiss-blog-1310999690.cos.ap-chengdu.myqcloud.com/cpu-cache/_MESI%E7%8A%B6%E6%80%81%E8%BD%AC%E6%8D%A2%E8%A1%A8%E6%A0%BC.webp)

上图描述一目了然。