# SSM、中间件面试题

## Spring、SpringBoot

**IOC详细说一说？**

**所谓 IOC ，就是由 Spring IOC 容器来负责对象的生命周期和对象之间的关系**

IOC：控制反转。类的创建等过程交给Spring去管理，用户只需要获得bean的实例来使用即可，不需要关心具体对象之间的依赖关系以及对象的生命周期

**Spring 的优点？**

**Spring用到了哪些设计模式？怎么用的？**

+ 单例模式
  + 默认每一个bean都是单例的
+ 工厂方法
  + FactoryBean接口
  + 实现了FactoryBean接口的bean是一类叫做factory的bean。其特点是，spring会在使用getBean()调用获得该bean时，会自动调用该bean的getObject()方法，所以返回的不是factory这个bean，而是这个bean.getOjbect()方法的返回值。
+ 适配器模式
  + SpringMVC中的适配器HandlerAdatper
  + HandlerAdatper根据Handler规则执行不同的Handler
+ 代理模式
  + AOP底层，就是动态代理模式的实现。
  + 切面在应用运行的时刻被织入。一般情况下，在织入切面时，AOP容器会为目标对象创建动态的创建一个代理对象。SpringAOP就是以这种方式织入切面的。
+ 模板方法模式
  + JdbcTemplate
  + 父类定义了骨架（调用哪些方法及顺序），某些特定方法由子类实现

**BeanFactory和FactoryBean有哪些区别？**

+ FactoryBean
  + 这种类型的Bean本身就是生产对象的工厂 
  + 本质是一个Bean
  + 当某些对象的实例化过程过于烦琐，通过XML配置过于复杂，使我们宁愿使用Java代码来完成这 个实例化过程的时候，或者，某些第三方库不能直接注册到Spring容器的时候，就可以实现org.spring- framework.beans.factory.FactoryBean接口

**BeanFactory和ApplicationContext有什么区别？**

ApplicationContext是BeanFactory的高级版本，在BeanFactory的基础上添加了一些高级特性。比如国际化，资源获取（AnnotationApplicationContext等）之类的高级特性。BeanFactory可以说是IOC容器的基础实现

**请解释Spring Bean的生命周期？**

https://www.jianshu.com/p/70b935f2b3fe

https://www.jianshu.com/p/1dec08d290c1

生命周期：

+ 实例化
  + createBeanInstance()
  + 拿到BeanDefinition，创建bean
+ 属性赋值
  + populateBean()
  + Spring利用依赖注入完成对IOC容器中各个组件的依赖关系赋值
+ 初始化
  + initializeBean()
  + 当BeanFactory将bean创建成功，并设置完成所有它们的属性后，我们想在这个时候去做出自定义的反应，比如检查一些强制属性是否被设置`成功，这个时候我们可以让我们的bean的class实现InitializingBean接口，以被触发
+ 销毁

**Spring支持的几种bean的作用域。**

+ singleton
+ prototype
+ session
+ request
+ global-session

**Spring框架中的单例Beans是线程安全的么？**

spring没有处理线程安全的问题

**Spring如何处理线程并发问题？**

spring没有处理线程安全的问题，线程安全用threadlocal

**AOP是什么？详细说一说？**

面向切面编程

**CGLIB和jdk动态代理有什么区别？优先用哪一个？**

**spring事务传播机制说一说？**

所谓事务传播机制，也就是在事务在多个方法的调用中是如何传递的，是重新创建事务还是使用父方法的事务？父方法的回滚对子方法的事务是否有影响？这些都是可以通过事务传播机制来决定的。

spring事务实际使用AOP拦截注解方法，然后使用动态代理处理事务方法，捕获处理过程中的异常，spring事务其实是把异常交给spring处理；

+ **REQUIRED**
  + 如果有事务则加入事务，如果没有事务，则创建一个新的（默认值）
+ **NOT_SUPPORTED**
  + Spring不为当前方法开启事务，相当于没有事务
+ **REQUIRES_NEW**
  + 不管是否存在事务，都创建一个新的事务，原来的方法挂起，新的方法执行完毕后，继续执行老的事务
+ **MANDATORY**
  + 必须在一个已有的事务中执行，否则报错
+ **NEVER**
  + 必须在一个没有的事务中执行，否则报错
+ **SUPPORTS**
  + 如果其他bean调用这个方法时，其他bean声明了事务，则就用这个事务，如果没有声明事务，那就不用事务
+ **NESTED**
  + 如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与REQUIRED类似的操作

**AOP和spring事务同时使用有没有什么需要注意的地方？**

事务失效原因：

1.如使用mysql且引擎是MyISAM，则事务会不起作用，原因是MyISAM不支持事务，可以改成InnoDB

2. 如果使用了spring+mvc，则context:component-scan重复扫描问题可能会引起事务失败。
3.  @Transactional 注解开启配置，必须放到listener里加载，如果放到DispatcherServlet的配置里，事务也是不起作用的。
4.  @Transactional 注解只能应用到 public 可见度的方法上。 如果你在 protected、private 或者 package-visible 的方法上使用 @Transactional 注解，它也不会报错，事务也会失效。
5.  Spring团队建议在具体的类（或类的方法）上使用 @Transactional 注解，而不要使用在类所要实现的任何接口上。在接口上使用 @Transactional 注解，只能当你设置了基于接口的代理时它才生效。因为注解是 不能继承 的，这就意味着如果正在使用基于类的代理时，那么事务的设置将不能被基于类的代理所识别，而且对象也将不会被事务代理所包装。

使用CGLIB实现动态代理的时候，不能将事务注解写在类所要实现的接口上

**Spring三级缓存？**

1，第一级缓存：单例缓存池singletonObjects。

2，第二级缓存：早期提前暴露的对象缓存earlySingletonObjects。

3，第三级缓存：singletonFactories单例对象工厂缓存

**spring怎么解决循环依赖的问题？**

三级缓存



**注解是什么？能不能自定义注解？**

**拦截器和过滤器有什么区别？**

**面向切面编程详细说一说？**

**解释一下Spring AOP里面的几个名词**

## SpringMVC

**springMVC的处理流程？**



**SpringMVC怎么样设定重定向和转发的？**

转发：forward:xxx.jsp

重定向：redirect:/xxx.jsp

**如何解决POST请求中文乱码问题，GET的又如何处理呢？**

**Spring MVC的异常处理 ？**

**SpringMvc的控制器是不是单例模式,如果是,有什么问题,怎么解决？**

## Mybatis

**Mybatis原理了解吗**

**#{}和${}的区别是什么？**

**Mybaits的优点：**

**Mybatis动态sql有什么用？执行原理？有哪些动态sql？**

**为什么说Mybatis是半自动ORM映射工具？它与全自动的区别在哪里？**

**Mybatis的一级、二级缓存:**

**什么是MyBatis的接口绑定？有哪些实现方式？**

## Redis

**跳表**

https://blog.csdn.net/pcwl1206/article/details/83512600

跳表(SkipList)：增加了向前指针的链表叫做指针。跳表全称叫做跳跃表，简称跳表。跳表是一个随机化的数据结构，实质是一种可以进行二分查找的有序链表。跳表在原有的有序链表上增加了多级索引，通过索引来实现快速查询。跳表不仅能提高搜索性能，同时也可以提高插入和删除操作的性能。

**redis有哪几种类型？**

五大基本类型

+ string
  + ![img](C:\Users\wyy45\Documents\Markdown\面试总结\图片\70)
  + 类似于java的Arraylist
  + 字符串对象底层数据结构实现为简单动态字符串（SDS）和直接存储，**但其编码方式可以是int、raw或者embstr，区别在于内存结构的不同**。
+ list
  + 在3.2版本之前，列表是使用ziplist和linkedlist实现的，在这些老版本中，当列表对象同时满足以下两个条件时，列表对象使用ziplist编码：

    + 列表对象保存的所有字符串元素的长度都小于64字节
    + 列表对象保存的元素数量小于512个
  + 当有任一条件 不满足时将会进行一次转码，使用linkedlist。
    
  + 双向链表
  + lpush+lpop=Stack(栈)
  + lpush+rpop=Queue（队列）
  + lpush+ltrim=Capped Collection（有限集合）
  + lpush+brpop=Message Queue（消息队列）
+ set
  + REDIS_ENCODING_INTSET
  + dict
+ zset
  + 两种存储方式
    + ziplist
      + 压缩列表的原理：压缩列表并不是对数据利用某种算法进行压缩，而是将数据按照一定规则编码在一块连续的内存区域，目的是节省内存。
    + skiplist与dict的结合
+ hash
  + 在实现结构上它使用二维结构，第一维是数组，第二维是链表
  + 拉链法
  + **ziplist编码**
  + **hashtable编码**

**redis跳表是什么结构？代码怎么实现？**

**redis通常有哪些使用场景？**

+ 缓存
+ 分布式锁

**redis持久化机制？有什么优劣？**

+ RDB
+ AOF

**redis事务是什么样的？**

**redis分布式锁怎么实现？**

主要依靠的命令：SETNX，如果存在则设置失败，如果不存在则设置成功

还需要一个过期时间和锁的续期，**watch dog自动延期机制**

在redis master实例宕机的时候，可能导致多个客户端同时完成加锁。

**redis挂了怎么办？**

如果只有一台redis，那么存在单点故障，如果没有持久化的话那么数据会全部丢失，没办法抢救

所以需要redis sentinel，一个哨兵，或者扩展redis架构，使用redis集群

集群的三种方式：

+ 主从模式
  + 将数据库复制多个副本以部署在不同的服务器上，这样即使有一台服务器出现故障，其他服务器依然可以继续提供服务
+ 哨兵模式
  + 哨兵的作用就是监控redis主、从数据库是否正常运行，主出现故障自动将从数据库转换为主数据库
+ 集群模式
  + 即每台redis存储不同的内容。
  + 集群至少需要3主3从，且每个实例使用不同的配置文件

**单线程的redis为什么这么快？**

1、完全基于内存，绝大部分请求是纯粹的内存操作，非常快速。数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)；

2、数据结构简单，对数据操作也简单，Redis中的数据结构是专门进行设计的；

3、采用单线程，避免了不必要的上下文切换和竞争条件，也不存在多进程或者多线程导致的切换而消耗 CPU，不用去考虑各种锁的问题，不存在加锁释放锁操作，没有因为可能出现死锁而导致的性能消耗；

4、使用多路I/O复用模型，非阻塞IO；

5、使用底层模型不同，它们之间底层实现方式以及与客户端之间通信的应用协议不一样，Redis直接自己构建了VM 机制 ，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求；

**Redis 为什么是单线程的，优点？**

因为Redis是基于内存的操作，**CPU不是Redis的瓶颈，Redis的瓶颈最有可能是机器内存的大小或者网络带宽**。既然单线程容易实现，而且CPU不会成为瓶颈，那就顺理成章地采用单线程的方案了。

采用单线程，避免了不必要的上下文切换和竞争条件，也不存在多进程或者多线程导致的切换而消耗 CPU，不用去考虑各种锁的问题，不存在加锁释放锁操作，没有因为可能出现死锁而导致的性能消耗；

**Redis 集群方案应该怎么做？都有哪些方案？**

主从、哨兵、集群

**有没有尝试进行多机redis 的部署？如何保证数据一致的？**

主从复制，sync，rdb，快照

**为什么Redis的操作是原子性的，怎么保证原子性的？**

redis是单进程单线程模型，所以操作都是原子性的。

原子性定义：操作不可再分，要么成功要么失败

单线程下redis命令执行也就只有这两种情况，要么成功要么失败，而且一条命令不会拆成两步执行，所以是原子的

**讲解下Redis线程模型？**

**redis的过期策略以及内存淘汰机制？**

**如何保证redis和数据库数据的一致性？（ Cache Aside Pattern）**

https://www.cnblogs.com/myseries/p/12068845.html

+  Cache Aside Pattern
  + 读的时候，先读缓存，缓存没有的话，就读数据库，然后取出数据后放入缓存，同时返回响应。
  + 更新的时候，**先更新数据库，然后再删除缓存**。
+ 延时双删策略
  + 在写库前后都进行redis.del(key)操作，并且设定合理的超时时间。
+ 异步更新缓存(基于订阅binlog的同步机制)
  + 读Redis：热数据基本都在Redis
  + 写MySQL:增删改都是操作MySQL
  + 更新Redis数据：MySQL的数据操作binlog，来更新到Redis
  + mysql更新之后，redis订阅者收到更新通知，然后更新redis

**redis操作系统调用？**

**Redis、zookeeper的选举机制了解吗？**

