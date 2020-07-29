# Java基础面试题

## 基础

### 基础知识

**排序算法以及他们的特点**

| 方法 |时间复杂度（平均）|空间复杂度|稳定性|
| ---- |    ----      |  ----  | ---- | 
|插入排序|      n^2     |   1   | yes  |
|希尔排序|     n^1.3    |   1   |  no  |
|选择排序|     n^2      |   1   |  no  |
|堆排序 |      nlog2n   |   1   | no   |
|冒泡排序|     n^2      |   1   | yes  |
|快速排序|     nlog2n   |nlog2n |  no  |
|归并排序|     nlog2n   |   n   | yes  |

**八种基本数据类型的大小，以及他们的封装类。**

| 类型    | 大小（byte）   | 包装类    |
| ------- | -------------- | --------- |
| boolean | 与编译环境有关 | Boolean   |
| byte    | 1              | Byte      |
| short   | 2              | Short     |
| int     | 4              | Integer   |
| long    | 8              | Long      |
| float   | 4              | Float     |
| double  | 8              | Double    |
| char    | 2              | Character |

**Switch能否用string做参数**

​	1.7之前：不可以

​	1.7之后：可以，实现的方法是对比String的hashcode

**redis操作系统调用？**

​	==比较的是引用是否相同，必须为同一个地址空间的值才会返回true

**Java的四种引用，强弱软虚，用到的场景。**

​	见JVM

### String

**String、StringBuffer与StringBuilder的区别**

+ String

  字符串广泛应用 在Java 编程中，在 Java 中字符串属于对象，Java 提供了 String 类来创建和操作字符串。

  String的值是不可变的，这就导致每次对String的操作都会生成**新的String对象**，这样不仅效率低下，而且大量浪费有限的内存空间

+ StringBuilder

  StringBuilder和StringBuffer是可变字符串

  StringBuilder不是线程安全的，但是访问效率比StringBuffer高

+ StringBuffer

  StringBuffer是线程安全的，但是访问效率不如StringBuilder

  单线程访问尽量使用StringBuilder

**能不能继承String类**

​	不能，String是一个final关键字声明的不可变类

**能不能自己定义一个String类？**

​	可以定义，但是虚拟机不会加载，具体参考类加载机制的双亲委派机制

### 类型转换

**类型转换应该注意的问题**

**double精度丢失**

**short s1 = 1; s1 = s1 + 1;有什么错? short s1 = 1; s1 += 1;有什么错?**



### 关键字、保留字

**final, finally, finalize 的区别？**

**wait()和sleep()的区别。**
   1.每个对象都有一个锁来控制同步访问。Synchronized关键字可以和对象的锁交互，来实现线程同步。sleep方法没有释放锁；wait方法释放了锁，使得其他线程可以使用同步控制块或者方法
   2.wait，notify，notifyAll只能在同步控制方法或者同步控制块里面使用；sleep可以在任何地方使用
   3.sleep必须捕获异常；而wait，notify，notifyAll不需要捕获异常
   4.sleep是线程类Thread的方法，导致此线程暂停执行指定时间，给执行机会给其他线程，但监控状态依然保持，到时候会自动恢复。调用sleep不会释放对象索。
   5.wait是Object的方法，对此调用wait方法导致本线程放弃对象锁，进入等待此对象的等待锁定池，只有针对此对象发出notify方法后本线程才进入对象锁定池准备活得对象锁进入运行状态。 


### 异常

**运行时异常与一般异常有何异同？**

1.运行时异常：都是RuntimeException类及其子类异常，如NullPointerException(空指针异常)、IndexOutOfBoundsException(下标越界异常)等，这些异常是不检查异常，程序中可以选择捕获处理，也可以不处理。这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。运行时异常的特点是Java编译器不会检查它，也就是说，当程序中可能出现这类异常，即使没有用try-catch语句捕获它，也没有用throws子句声明抛出它，也会编译通过。
   2.非运行时异常 （编译异常）：是RuntimeException以外的异常，类型上都属于Exception类及其子类。从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如IOException、SQLException等以及用户自定义的Exception异常，一般情况下不自定义检查异常。

**Excption与Error包结构。OOM你遇到过哪些情况，SOF你遇到过哪些情况**

**try catch finally，try里有return，finally还执行么？**



## 对象拷贝

**写 clone()方法时，通常都有一行代码，是什么？**

**HashCode存储的是什么？**

**为什么重写HashCode要重写Equals**

## Serialize



## 反射

**什么是反射？**

java反射机制允许我们程序员在程序运行的时候获取一个类的各种内部信息，比如说modifiers（诸如public, static 等）、superclass（例如Object）、实现之interfaces（例如Cloneable），也包括fields和methods的所有信息。更重要的是我们还能够修改这些信息。

forname和classloader的区别

**反射的原理**

Java的反射就是利用上面第二步加载到jvm中的.class文件来进行操作的。.class文件中包含java类的所有信息，当你不知道某个类具体信息时，可以使用反射获取class，然后进行各种操作。

**反射的常见方法**

+ getName()：获得类的完整名字。
+ getFields()：获得类的public类型的属性。
+ getDeclaredFields()：获得类的所有属性。包括private 声明的和继承类
+ getMethods()：获得类的public类型的方法。
+ getDeclaredMethods()：获得类的所有方法。包括private 声明的和继承类
+ getMethod(String name, Class[] parameterTypes)：获得类的特定方法，name参数指定方法的名字parameterTypes 参数指定方法的参数类型。
+ getConstructors()：获得类的public类型的构造方法。
+ getConstructor(Class[] parameterTypes)：获得类的特定构造方法，parameterTypes 参数指定构造方法的参数类型。
+ newInstance()：通过类的不带参数的构造方法创建这个类的一个对象。
  

## 其它

**java迭代器**

(1) 使用方法iterator()要求容器返回一个Iterator。第一次调用Iterator的next()方法时，它返回序列的第一个元素。注意：iterator()方法是java.lang.Iterable接口,被Collection继承。

(2) 使用next()获得序列中的下一个元素。

(3) 使用hasNext()检查序列中是否还有元素。

(4) 使用remove()将迭代器新返回的元素删除。

+ hasNext():判断当前元素是否存在，并没有指向的移动

+ next():返回当前元素， 并指向下一个元素

**Java1.7与1.8新特性。**

## 面向对象

### 基本概念

**封装、继承、多态的概念**

**solid原则**

+ 单一职责原则
  + 一个类应该只有一个发生变化的原因
+ 开放闭合原则
  + 一个软件实体，如类、模块和函数应该对扩展开放，对修改关闭
+ 依赖倒置原则
  + 高层模块不应该依赖底层模块，两者都应该依赖其抽象。
  + 抽象不应该依赖细节。
  + 细节应该依赖抽象。
+ 迪米特法则
  + 两个软件实体无须直接通信，那么就不应当发生直接的相互调用
+ 接口隔离原则
  + 客户端不应该依赖它不需要的接口。
+ 里氏替换原则
  + 所有引用基类的地方必须能透明地使用其子类的对象。

**面向对象的特征有哪些方面？**

**面向对象和面向过程的区别**

**String 是最基本的数据类型吗?**

**super()与 this()的区别？**

**什么是 java 序列化，如何实现 java 序列化？**

**Overload 和 Override 的区别。Overload 的方法是否可以改变返回值的类型?**

+ **方法重载：**本类出现的类名相同，参数列表不同的方法，与返回值无关。

**java 中实现多态的机制是什么？**

​	动态分派机制。

**静态变量和实例变量的区别？**

### 类相关知识

**Interface与abstract类的区别**

**组合与继承有什么区别**

**作用域 public,protected,private,以及不写时的区别？**

+ public： Java语言中访问限制最宽的修饰符，一般称之为“公共的”。被其修饰的类、属性以及方法不
  　　　　　仅可以跨类访问，而且允许跨包（package）访问。

  ​					**大家都可以访问**

+ private: Java语言中对访问权限限制的最窄的修饰符，一般称之为“私有的”。被其修饰的类、属性以
  　　　　　及方法只能被该类的对象访问，其子类不能访问，更不能允许跨包访问。

  ​			  	 **只有自己可以访问**

+ protect: 介于public 和 private 之间的一种访问修饰符，一般称之为“保护形”。被其修饰的类、
  　　　　　属性以及方法只能被类本身的方法及子类访问，即使子类在不同的包中也可以访问。

  ​					**本类以及子类可以访问，即使包不同**

+ default：即不加任何访问修饰符，通常称为“默认访问模式“。该模式下，只允许在同一个包中进行访
  　　　　　问。

  ​					**同一包内可以访问**

**继承时候类的执行顺序问题**

### 常见类



**int 和 Integer 有什么区别？**

**Integer缓存是怎么回事？其他的包装类也有吗？**

**什么是不可变类？有什么特点？**

**日期类型**



## 集合框架

**集合框架的类和接口的结构是怎样的？**

**集合的作用是什么?**

**集合的通用方法有那些?通用方法是什么?(操作)**

**说出 ArrayList,Vector, LinkedList 的存储性能和特性 **

### HashMap

**红黑树有什么特点？AVL树？B+树？**

红黑树：

+ 性质1. 节点是红色或黑色。 

+ 性质2. 根节点是黑色。 

+ 性质3.所有叶子都是黑色。（叶子是NUIL节点） 

+ 性质4. 每个红色节点的两个子节点都是黑色。（从每个叶子到根的所有路径上不能有两个连续的红色节点）

+ 性质5.. 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点

AVL树：



**哈希冲突是什么？怎么解决？有什么优缺点？**

​	如果两个不同对象的hashCode相同，这种现象称为hash冲突。

​	解决哈希冲突的办法：

+ 开放定址法

  + 探查序列的方法
    + 线性探查
      + 冲突发生时，顺序查看表中下一单元，直到找出一个空单元或查遍全表
    + 二次探查
      + 这种方法的特点是：冲突发生时，在表的左右进行跳跃式探测，比较灵活

+ 拉链法

  + 拉链法的优点

    与开放定址法相比，拉链法有如下几个优点：

    + ①拉链法处理冲突简单，且无堆积现象，即非同义词决不会发生冲突，因此平均查找长度较短；

    + ②由于拉链法中各链表上的结点空间是动态申请的，故它更适合于造表前无法确定表长的情况；

    + ③开放定址法为减少冲突，要求装填因子α较小，故当结点规模较大时会浪费很多空间。而拉链法中可取α≥1，且结点较大时，拉链法中增加的指针域可忽略不计，因此节省空间；

    + ④在用拉链法构造的散列表中，删除结点的操作易于实现。只要简单地删去链表上相应的结点即可。

  + 拉链法的缺点

    + 指针需要额外的空间，故当结点规模较小时，开放定址法较为节省空间，而若将节省的指针空间用来扩大散列表的规模，可使装填因子变小，这又减少了开放定址法中的冲突，从而提高平均查找速度。

+ 再哈希法

  + 再散列法其实很简单，就是再使用哈希函数去散列一个输入的时候，输出是同一个位置就再次散列，直至不发生冲突位置

+ 建立公共溢出区
  
  + 将哈希表分为基本表和溢出表两部分，凡是和基本表发生冲突的元素，一律填入溢出表。
  
  拉链法和开放定址法优劣总结：
  
  + 拉链法平均查找长度较短，链表节点空间动态申请，适合无法确定表长的情况
  + 开放定址法要求装载因子比较小，所以比较浪费空间
  + 拉链法指针需要额外空间，如果节省空间又会增加查找时间，所以这是一种空间换时间的操作

**HashMap 的数据结构？**

​	采用的是数组+链表的结构，使用拉链法解决hash冲突。JDK1.8之后，如果当冲突的元素的个数超过八个，则会转化为红黑树，小于六个则转化回链表

**HashMap怎么扩容？ **

​	JDK1.8 resize(）函数为扩容函数。如果当前的元素容量大于hashmap容量装载因子的时候，触发扩容操作。

​	JDK1.7 rehash() 

**HashMap扩容计算方法**

由于扩容数组的长度是 2 倍关系，所以对于假设初始 tableSize = 4 要扩容到 8 来说就是 0100 到 1000 的变化（左移一位就是 2 倍），**在扩容中只用判断原来的 hash 值与左移动的一位（newtable 的值）按位与操作是 0 或 1 就行，0 的话索引就不变，1 的话索引变成原索引加上扩容前数组**

**HashMap初始容量计算？**

​	如果不手动设置大小，初始容量默认为16；

​	tableSizeFor(int cap)函数，简单来说，大概分为以下几步：

+ int n = cap - 1;
+ n |= n >>> 1;
+ n | = n >>>2;
+ n | = n >>>4;
+ n | = n >>>8;
+ n | = n >>>16;

**使用自定义的类来作为HashMap的Key需要注意什么？**

用自定义类作为key，必须重写equals()和hashCode()方法。如果不重写的话，比较的将是两个对象是否相同，而不是值是否相等

自定义类中的equals() 和 hashCode()都继承自Object类。

Object类的hashCode()方法返回这个对象存储的内存地址的编号。

而equals()比较的是内存地址是否相等。

**JDK1.8Hash的实现？为什么这么实现？**

```java
(h = key.hashCode()) ^ (h >>> 16)
```

hashcode高位与低位异或计算出hash值。这样计算的好处在于：提高散列度，减少碰撞次数。

（1）首先获取对象的hashCode()值，然后将hashCode值右移16位，然后将右移后的值与原来的hashCode做异或运算，返回结果。（其中h>>>16，在JDK1.8中，优化了高位运算的算法，使用了零扩展，无论正数还是负数，都在高位插入0）。

（2）在putVal源码中，我们通过(n-1)&hash获取该对象的键在hashmap中的位置。（其中hash的值就是（1）中获得的值）其中n表示的是hash桶数组的长度，并且该长度为2的n次方，这样(n-1)&hash就等价于hash%n。因为&运算的效率高于%运算。

**扰动函数是干什么用的？**

见上一问，作用就是降低碰撞概率

**装载因子是什么？为0或为1会发生什么情况？**

加载因子是表示Hsah表中元素的填满的程度.若:加载因子越大,填满的元素越多,好处是,空间利用率高了,但:冲突的机会加大了.反之,加载因子越小,填满的元素越少,好处是:冲突的机会减小了,但空间浪费多了.

为0：一直扩容，直到达到最大值2的31次方-1

为1：元素满了才会扩容

**什么是树化？会还原吗？为什么需要树化或者还原？**

如果当冲突的元素的个数超过八个，则会转化为红黑树，小于六个则转化回链表。

树化的原因在于降低查找时间。

**HashMap与HashTable的区别？与Collections.synchronizedMap()有什么区别？与ConcurrentHashMap有什么区别？**

HashMap：线程不安全

HashTable： 线程安全，但是锁的粒度很粗，粗暴地在各个关键地方法上加上了Synchronized关键字

Collections.synchronizedMap()：锁的粒度比HashTable更细一些。HashTable悲观地必须所有方法都得进行同步，Collections.synchronizedMap()使用同步代码块，粒度更细。

ConcurrentHashMap：JUC里面的类，JDK1.7使用分段锁机制实现线程安全，JDK1.8采用CAS+synchronized实现线程安全

### Arrays,Collections

## 多线程

### 基础

**如何在Java中实现线程？**

+ 继承Thread
+ 实现Runnable
+ 实现Callable

**有哪些不同的线程生命周期？**

+ new
+ running
+ wait
+ wait-time
+ blocked
+ terminal

**Java线程状态转换**

**什么是线程安全？有哪些类是线程安全的？**

**Java中用到的线程调度算法是什么？**

抢占式调度

**为什么wait, notify 和 notifyAll这些方法不在thread类里面？**

**讲下join,yield方法的作用,以及什么场合用它们？**

**为什么你应该在循环中检查等待条件?**

防止虚假唤醒

**如何在两个线程间共享数据？**

**Java中Runnable和Callable有什么不同？**

callable可以有返回值，runnable不行

### volatile

可见性、禁止指令重排序

### JUC

**CAS**

**Java中CyclicBarrier 和 CountDownLatch有什么不同？**

### AQS

AQS：同步队列器

JUC包下的大部分锁的实现都依赖于AQS

核心：CAS操作 + state变量。双向链表的阻塞队列

以ReentrantLock的加锁和解锁方法为例：

```Java
final void lock() {
    //CAS操作，修改state
    if (compareAndSetState(0, 1))
        setExclusiveOwnerThread(Thread.currentThread());
    else
        //看下面第三个方法
        acquire(1);
}

//AQS里的CAS方法
protected final boolean compareAndSetState(int expect, int update) {
        // See below for intrinsics setup to support this
        return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
    }

final boolean nonfairTryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            int c = getState();
            if (c == 0) {
                //如果state = 0，则不排队了，直接执行
                if (compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
   				//如果是重入，即当前线程已经获得了锁，则重入
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0) // overflow
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }

public final boolean release(int arg) {
        if (tryRelease(arg)) {
            Node h = head;
            if (h != null && h.waitStatus != 0)
                //唤醒阻塞队列里面的下一个节点
                unparkSuccessor(h);
            return true;
        }
        return false;
    }


protected final boolean tryRelease(int releases) {
            int c = getState() - releases;
    //A线程尝试释放B线程持有的锁是不可以的
            if (Thread.currentThread() != getExclusiveOwnerThread())
                throw new IllegalMonitorStateException();
            boolean free = false;
    //c等于0了说明这是最后一层锁，可重入锁需要加锁多少次就解锁多少次
            if (c == 0) {
                free = true;
                setExclusiveOwnerThread(null);
            }
    //修改state
            setState(c);
            return free;
        }
```

可以看到这里面的加锁和解锁方法始终围绕着state这个变量来运转。

如果state = 0了，说明没有加锁，大于0则已经被加锁，会记录当前获得锁的线程号。

如果获取锁失败，则进入阻塞队列，Unsafe的park方法阻塞线程，定期检查自己是否需要延长休眠时间（如果被中断就退出），CAS阻塞。

所以AQS适合等待线程少的方法，不然占CPU

### 锁

**锁升级过程**

**对象锁和类锁**

**如何控制某个方法允许并发访问线程的大小？**

readwritelock，或者自己实现一个锁

**什么是自旋锁？**

**什么是互斥锁？**



### 线程池、阻塞队列

**Java线程池？线程池的七大参数详解？**

**基础的线程池有哪几种？使用场景有哪些？**

+ newCachedThreadPool创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。
+ newFixedThreadPool 创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。
+ newScheduledThreadPool 创建一个定长线程池，支持定时及周期性任务执行。
+ newSingleThreadExecutor 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。

**在Java中Executor、ExecutorService、Executors的区别？**

**BlockingQueue的原理**

### ThreadLocal

**什么是ThreadLocal?**

每一个线程内部有一个TreadLocalMap，存储线程内部安全的变量

### ConcurrentHashMap

**concurrentHashMap的源码理解以及内部实现原理，为什么他是同步的且效率高**

### 其它

**什么是Java线程转储(Thread Dump)，如何得到它？**

## IO、NIO、Netty

**什么是序列化？**

**序列化框架有哪些？**

**Dubbo支持哪些序列化协议？**

**TCP粘包和拆包问题了解吗？**

**怎么解决粘包问题？**

**Netty怎么解决拆包问题？**

 