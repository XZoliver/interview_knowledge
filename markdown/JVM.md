@[TOC]
GC需要完成三件事：
- 哪些内存需要回收
- 什么时候回收
- 如何回收

##  	对象已死吗
### 引用计数法
这个算法是这样判断对象是否存活的：

给对象中添加一个引用计数器，每当有一个地方引用它时，计数器就加1；当引用失效时，计数器值就减1；任何时刻计数器为0的对象就是不可能再被使用的。

**它无法解决对象之间循环引用的问题，同时虚拟机也不是通过引用计数法来判断对象是否存活的**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200702011046677.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNDc1OTE0,size_16,color_FFFFFF,t_70)

简单示例： 对象objA和objB都有属性instance,赋值令objA.instance = objB 及 objB.instance = objA, 除此之外，这两个对象再无其他引用，实际上着两个对象已经不可能再被访问，但是他们因为互相引用而导致对方的引用计数都不为0，于是引用技术算法无法通知GC收集器去回收它们。

### 可达性分析
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200702011115471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNDc1OTE0,size_16,color_FFFFFF,t_70)
以``GC Roots``的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为“引用链”，当一个对象到`GC Roots`没有任何引用链相连（图论中即为从`GC Roots`到这个对象不可达）时，则证明对象是不可用的。

Java中，可作为`GC Roots`的对象包括以下：

- 虚拟机栈（栈帧中的本地变量表）中引用的对象
- 方法区中类静态属性引用的对象
- 方法区中常量引用的对象
- 本地方法栈中引用的对象

## 引用的类型

无论是通过引用计数法去判断对象的引用数量，还是通过可达性分析算法去判断对象的引用链是否可达，这些都与“引用”有关。 Java中的引用有四种：

- 强引用（StrongReference）

  在代码中普遍存在，类似 Object obj = new Object()这类引用; 只要强引用还存在，垃圾收集器永远不会回收被它引用的对象。

- 软引用  (SoftReference)

  有用但非必要的对象，“食之无味，弃之可惜”。在内存空间还足够时，则能保存在内存之中，如果内存空间在垃圾收集后还是非常紧张，则可以抛弃这些对象。

- 弱引用  (WeakReference)

  非必需的对象，被它引用的对象只能生存到下一次垃圾收集发生之前。

  当垃圾收集器工作时，无论当前内存是否足够，都会回收掉被弱引用关联的对象。

- 虚引用  (PhantomReference)

  最弱的一种引用关系，随时都可能被回收，也无法通过虚引用来取得一个对象实例。 为一个对象设置虚引用关联的唯一目的时，在这个对象被收集器回收时收到一个系统通知。

  ## 垃圾收集算法

  ### 标记-清除算法（Mark-Sweep）
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020070201115166.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNDc1OTE0,size_16,color_FFFFFF,t_70)
  该算法分为“标记”和“清除”两个阶段： 首先需要标记出所有需要回收的对象，在标记完成后统一回收所有被标记的对象。

  问题： 会产生大量的内存碎片，空间碎片太多可能导致以后在程序运行过程中需要分配较大对象时，无法找到足够的连续内存而不得不提前触发另一个垃圾收集动作。

  ### 复制算法
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200702011213824.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNDc1OTE0,size_16,color_FFFFFF,t_70)
  问了解决大量的内存碎片问题，复制算法就出现了。它将可用内存划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活的对象复制到另一块上面，然后再把已使用过的内存空间一次清理掉。

  问题： 

  - 直接将内存缩小了一半
  - 在对象存活率较高情况下，复制的代价比较大

  现在很多虚拟机都采用这种收集算法来回收新生代，研究表明，新生代中的对象98%都是朝生夕死的，所以并不需要按照1：1的比例来划分内存空间。而是将内存划分为一块大的Eden区和两块较小的Survivor区，比例为8:1:1。也就是说新生代中可用内存空间变成总容量的90%, 剩下的10%的空间用于放置每次存活的对象。但我们没有办法保证，每次存活的对象比例都小于10%，因此当Survivor空间不够用时，需要依赖其他内存进行分配担保。

  ### 标记-整理算法
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200702011242994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNDc1OTE0,size_16,color_FFFFFF,t_70)
  先标记出需要回收的对象，之后将存活的对象都向一端移动，然后直接清理掉边界以外的内存。

  ### 分代收集算法

  将Java堆分成新生代和老年代。

  新生代： 大批对象死去，少量对象存活，可采用复制算法。（只需要付出少量存活对象的复制成本就可以完成收集）

  老年代： 少量对象死去，大批对象存活，采用标记-清除或标记整理算法。（没有额外的空间对它进行担保）

## 垃圾收集器

  ###  CMS (Concurrent Mark Sweep)

  从（Mark Sweep）可以看出， CMS收集器是基于**"标记-清除"**算法实现的，它的运作过程包含以下四个步骤：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200709172046565.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNDc1OTE0,size_16,color_FFFFFF,t_70)

  ``初始标记  并发标记 重新标记 并发清除``

  `初始标记`仅仅是标记以下GC Roots能直接关联到的对象，速度很快

  `并发标记`阶段就是进行GC Roots Tracing, 即对象的可达性分析过程

  `重新标记`阶段是为了修正并发标记期间因用户程序继续运作而产生变动的那一部分对象的标记记录

  `并发清理`阶段是为了将上面三个阶段标记出的对象进行清理。

  其中，带“并发“字样的并发标记和并发清除阶段都是可以与用户线程一起工作，初始标记、重新标记仍需要“Stop The World”，但这两个阶段相比并发阶段的时间可以忽略不计，其中并发标记和并发清除阶段才是最耗时的，但是可以用用户线程并发。因此，**整体上看，CMS收集器的内存回收过程是与用户线程一起并发执行的。**

  #### 优点

  `并发收集 低停顿`

  #### 缺点

  1. 基于`“标记-清除”`的垃圾回收算法，会产生`大量的内存碎片`。 空间内存碎片过多时，老年代分配大对象时会因无法找到足够大的连续空间而不得不提前触发一次Full GC。
  2. 无法处理`浮动垃圾`，可能会出现"**Concurrent Mode Failure**"失败而导致；另一次Full GC的产生。由于在并发清理阶段用户线程还在运行着，伴随着程序的运行自然就会有新的垃圾不断产生，这一部分垃圾出现在标记过程之后，**CMS无法在当次收集中处理他们，只好待下一次GC时再清理掉，这一部分就是“浮动垃圾”。**要是CMS运行期间预留的内存无法满足程序的需要，就会出现**"Concurrent Mode Failure"**。

  ### G1

  四个步骤： `初始标记 并发标记 最终标记 筛选回收`
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200709172115187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNDc1OTE0,size_16,color_FFFFFF,t_70)
  前三个阶段与CMS很类似，

  `初始标记`仅仅标记一下GC Roots能直接关联到的对象，需要停顿线程，但耗时很短，想较与其他阶段可忽略不计。

  `并发标记`是GCRoots Tracing，对堆中的对象进行可达性分析，找出存活的对象，耗时较长，可与用户线程并发执行。

  `最终标记`是为了修正在并发标记期间因用户线程继续运作而导致标记产生变动的那一部分标记记录，虚拟机将这段对象变化记录在线程`Remembered Set Logs`里，并将数据合并到`Remembered  Set`中。

  `筛选回收`是对各个Region的回收价值和成本进行排序，根据用户所期望的GC停顿时间来指定回收计划。

  #### 优点

  1. **整体上看是“标记-整理”，局部是“复制”算法**，运作期间不会产生内存碎片，收集后能提供规整的可用内存。
  2. **可预测的停顿时间**，根据每个Region的回收价值和成本进行排序去维护一个优先列表，每次根据允许的停顿时间，优先回收价值最大的Region。

## 新生代中Eden区和两个Survivor区的解释

#### 为什么会有年轻代

为什么要把**堆分代**？这完全是出于GC时的性能去考虑的。很多对象是“朝生夕死”的，我们一般根据对象的存活时间将堆分为新生代和老年代。

**新生代**中的对象存活时间短，对象几乎朝生夕死，一次GC后对象的存活率低。GC在新生代工作时会回收大量的对象，进而腾出很大的空间。

**老年代**中对象存活时间长，一次GC后对象的存活率高。GC工作后依然有大量的对象存活，可用来腾出的空间很少。

那我们如果不进行分区，每次GC工作时都要全堆扫描，因为我们不知道哪些对象有用，哪些对象没用。但要是将堆分成老年代和新生代的话，每次在新生代区进行GC（而不用全堆扫描）总能腾出大量的空间，这样优化了GC性能。

#### 新生代中的GC

JVM将年轻代分成了三部分： 一个较大的`Eden`区和两个较小的`Survivor`区（叫 from 和 to），默认比例为`8:1:1`，新生代中80%的对象是朝生夕死的，使用的垃圾回收算法是`复制算法`。大致过程如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200709172143507.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNDc1OTE0,size_16,color_FFFFFF,t_70)
新创建的对象会被放在`Eden`区，这些对象经过第一次`Minor GC`后，如果仍然存活，将会被移到`Survivor`区。对象在Survivor区中每熬过一次Minor GC，年龄就会增加1岁，当它的年龄增加到一定程度时，就会被移动到年老代中。

在GC开始的时候，对象只会存在于Eden区和名为“From”的Survivor区，Survivor区“To”是空的。紧接着进行GC，Eden区中所有存活的对象都会被复制到“To”，而在“From”区中，仍存活的对象会根据他们的年龄值来决定去向。年龄达到一定值(年龄阈值，可以通过-XX:MaxTenuringThreshold来设置)的对象会被移动到年老代中，没有达到阈值的对象会被复制到“To”区域。经过这次GC后，Eden区和From区已经被清空。这个时候，“From”和“To”会交换他们的角色，也就是新的“To”就是上次GC前的“From”，新的“From”就是上次GC前的“To”。不管怎样，都会保证名为To的Survivor区域是空的。Minor GC会一直重复这样的过程，直到“To”区被填满，“To”区被填满之后，会将所有对象移动到年老代中。

#### 一个对象这一辈子

我是一个普通的java对象，我出生在Eden区，在Eden区我还看到和我长的很像的小兄弟，我们在Eden区中玩了挺长时间。有一天Eden区中的人实在是太多了，我就被迫去了Survivor区的“From”区，自从去了Survivor区，我就开始漂了，有时候在Survivor的“From”区，有时候在Survivor的“To”区，居无定所。直到我18岁的时候，爸爸说我成人了，该去社会上闯闯了。于是我就去了年老代那边，年老代里，人很多，并且年龄都挺大的，我在这里也认识了很多人。在年老代里，我生活了20年(每次GC加一岁)，然后被回收。

## Minor GC 和 Full GC

- **Minor GC**：发生在新生代上，因为新生代对象存活时间很短，因此 **Minor GC 会频繁执行，执行的速度一般也会比较快。**
- **Full GC**：发生在老年代上，**老年代对象其存活时间长，因此 Full GC 很少执行，执行速度会比 Minor GC 慢很多。**

#### Minor GC 的触发条件

对于 Minor GC，其触发条件非常简单，当 Eden 空间满时，就将触发一次 Minor GC。

#### Full GC 的触发条件

而 Full GC 则相对复杂，有以下条件：

1. **调用 System.gc()**
    只是建议虚拟机执行 Full GC，但是虚拟机不一定真正去执行。不建议使用这种方式，而是让虚拟机管理内存。

2. **老年代空间不足**
    老年代空间不足的常见场景为前文所讲的大对象直接进入老年代、长期存活的对象进入老年代等。


3. **空间分配担保失败**
    使用复制算法的 Minor GC 需要老年代的内存空间作担保，如果担保失败会执行一次 Full GC。

4. **Concurrent Mode Failure（CMS收集器产生的内存碎片后，老年代无法放入连续大对象）**

   执行 CMS GC 的过程中同时有对象要放入老年代，而此时老年代空间不足（可能是 GC 过程中浮动垃圾过多导致暂时性的空间不足），便会报 Concurrent Mode Failure 错误，并触发 Full GC。

#### 内存分配与回收策略

   1. **对象优先在 Eden 分配**

      大多数情况下，对象在新生代 Eden 区分配，当 Eden 区空间不够时，发起 Minor GC。

   2. **大对象直接进入老年代**

      大对象是指需要连续内存空间的对象，最典型的大对象是那种很长的字符串以及数组。经常出现大对象会提前触发垃圾收集以获取足够的连续空间分配给大对象。

   3. **长期存活的对象进入老年代**

      为对象定义年龄计数器，对象在 Eden 出生并经过 Minor GC 依然存活，将移动到 Survivor 中，年龄就增加 1 岁，增加到一定年龄则移动到老年代中。

   4.  **动态对象年龄判定**
      虚拟机并不是永远地要求对象的年龄必须达到 MaxTenuringThreshold 才能晋升老年代，如果在 Survivor 中相同年龄所有对象大小的总和大于 Survivor 空间的一半，则年龄大于或等于该年龄的对象可以直接进入老年代，无需等到 MaxTenuringThreshold 中要求的年龄。

### Stop The World

GC的时候为什么要StopTheWorld（全局停顿）？

在GC的时候，进行标记过程时，可达性分析必须要在一个可以保证一致性的快照中执行！也就是说，不可以出现在分析过程中对象的引用关系还在不断变化的情况，该点不满足的话，分析结果的准确性就不能够得到保证！

### 参考链接

《深入理解Java虚拟机》

[新生代Eden与两个Survivor区的解释](https://blog.csdn.net/lojze_ly/article/details/49456255)

[JVM:内存分配与回收策略？Full GC 的触发条件？StopTheWorld ?](https://blog.csdn.net/Fly_as_tadpole/article/details/85011677)