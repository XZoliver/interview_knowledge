## Java中的多态

要了解多态，我们先来看一下继承。

## 继承

```Java
public class inheritExmple {
    public static void main(String[] args) {    
        //向上转型（子类向父类去转型，隐士转换）
        Person person = new Child();
        person.sleep();
        person.work();
        //person.gotoschool();  无法调用子类特有的方法

        //向下转型（父类向子类去转，这里父类引用必须指向子类实例才能转成功，否则会抛出运行时异常）
        //Child child1 = (Child) new Person();
        //Exception in thread "main" java.lang.ClassCastException: Person cannot be cast to Child
        Child child1 = (Child) person;
        child1.gotoSchool();
        child1.work();
        child1.sleep();
    }
}

class Person{
    public void sleep(){
        System.out.println("Sleep");
    }
    public  void work(){
        System.out.println("working");
    }
}

class Child extends Person{
    public void work(){ //重写的一个父类的方法
        System.out.println("Learning");
    }
    public void gotoSchool(){ //子类所特有的一个方法
        System.out.println("Goto school");
    }
}
```

#### 向上转型

```java
 //向上转型（子类向父类去转型，隐士转换）
        Person person = new Child();
        person.sleep();  //Sleep
        person.work();	//Learning
        //person.gotoschool();  无法调用子类特有的方法
```

重要的点：

1. 向上转型是用一个父类引用去接受子类对象，```Person person = new Child();```可以调用父类的一些方法，如果该方法被子类重写，则调用的应该时子类重写的方法（实际上时因为你new出的实例还是子类的对象）但是它已经是一个父类引用了，所以无法去调用子类特有的方法，即向上转型时会遗失子类特有的一些方法。

2. 那既然向上转型会丢失子类特有的属性和方法，那为什么要转呢，直接用子类引用去接受子类对象不好吗？看我上面举的多态的一个栗子

   ```java
   class Human{
       void sayHello(){
           System.out.println("Hello, Human");
       }
   }
   
   class Woman extends Human{
       void sayHello(){
           System.out.println("Hello, Woman");
       }
   }
   
   class Man extends  Human{
       void sayHello(){
           System.out.println("Helllo, Man");
       }
   }
   
   public  static void sayHello(Human human){
           human.sayHello();
   }
   /*output: 
   Hello, Human
   Hello, Woman
   Helllo, Man
   */
   ```

   sayHello()这个方法，我直接用父类引用作为形参，那它就可以接受所有的父类和子类的任何对象了，不用你分别用子类引用作为形参去分别创建方法。这里传参其实就是向上转型（隐式转换），将接受到的Woman、Man类型的对象转为Human对象，而且因为多态重写了那个方法，因为调用的时候还是各子类重写后的方法。**这也满足里氏替换原则，只要有父类出现的地方，都可以用子类来替代，而且不会出现任何错误和异常。**

#### 向下转型

```java
//向下转型（父类向子类去转，需要强制类型转换，这里父类引用必须指向子类实例才能转成功，否则会抛出运行时异常）
        //Child child1 = (Child) new Person();
        //Exception in thread "main" java.lang.ClassCastException: Person cannot be cast to Child
        Child child1 = (Child) person;
        child1.gotoSchool();
        child1.work();
        child1.sleep();
```

重要的点：

1. 向下转型是将父类引用强制转成子类引用，需要强制转换类型，这里能成功转换的条件是：

   **该父类引用一定是要指向一个子类对象**，否则的话，会报一个运行时异常：```Exception in thread "main" java.lang.ClassCastException: Person cannot be cast to Child```

## 多态(重载是静态分派 重写是动态分派)

**多态是同一个行为具有多个不同表现形式或形态的能力。**

**多态实现的必要条件**:

1. **子类必须继承父类** 

2. **必须有重写**(重新定义的父类方法，使得父类和子类对同一行为的表现形式各不相同)

3. **父类引用指向子类对象**

**多态的实现途径**:

多态的实现途径有三种：重写、重载、接口实现，虽然它们的实现方式不一样，但是核心都是：**同一行为的不同表现形式**。

>  **1. 重写**
>
> 重写，指的是子类对父类方法的重新定义，但是子类方法的参数列表和返回值类型，必须与父类方法一致！所以可以简单的理解，重写就是子类对父类方法的核心进行重新定义。
>
> **2. 重载**
>
> 重载，指的是在一个类中有若干个**方法名相同，但参数列表不同的情况，返回值可以相同也可以不同**的方法定义场景。也可以简单理解成，同一行为（方法）的不同表现形式。
>
> **3. 接口实现**
>
> 接口，是一种无法被实例化，但可以被实现的抽象类型，是抽象方法的集合，多用作定义方法集合，而方法的具体实现则交给继承接口的具体类来定义。所以，**接口定义方法，方法的实现在继承接口的具体类中定义，也是对同一行为的不同表现形式**。

**在虚拟机中多态如何表现**

前文我们知道，java文件在经过javac编译后，生成class文件之后在JVM中再进行编译后生成对应平台的机器码。而JVM的编译过程中体现多态的过程，在于选择出正确的方法执行，这一过程称为**方法调用**。

**方法调用的唯一任务是确定被调用方法的版本**，暂时还不涉及方法内部的具体运行过程。（注：方法调用不等于方法执行）

下面我们主要看一下多态中的重载和重写

```java
class Human{
    void sayHello(){
        System.out.println("Hello, Human");
    }
}

class Woman extends Human{
    void sayHello(){
        System.out.println("Hello, Woman");
    }
}

class Man extends  Human{
    void sayHello(){
        System.out.println("Helllo, Man");
    }
}
```

#### 重载


```java
public class Dispatch {
    public static void main(String[] args) {
        Human human = new Human();
        sayHello(human);

        Human woman = new Woman();
        sayHello(woman);

        Human man = new Man();
        sayHello(man);
        // 变量man的静态类型 = 引用类型 = Human：不会被改变、在编译器可知
 		// 变量man的动态类型 = 实例对象类型 = Man：会变化、在运行期才可知
    }
    //重载是静态分配
    public static void sayHello(Human human){
        System.out.println("hello human");
    }
    public static void sayHello(Woman woman){
        System.out.println("hello woman");
    }
    public static void sayHello(Man man){
        System.out.println("hello man");
    }
}
/*output: 
hello human
hello human
hello human
*/  
```

重载，指的是在一个类中有若干个**方法名相同，但参数列表不同的情况，返回值可以相同也可以不同**的方法定义场景。也可以简单理解成，同一行为（方法）的不同表现形式。

我们把```Human man = new Man();```中的“Human”称为变量man的**静态类型**（Static Type），而后面的"Man"称为变量的**实际类型**（Actual Type），二者的区别在于，**静态类型是在编译期可知的；而实际类型的结果在运行期才能确定**，编译期在编译程序时并不知道一个对象的实际类型是什么。

#### 静态分派

根据 变量的**静态类型** 进行方法分派的行为。

1.  即根据 变量的静态类型 确定执行哪个方法
2.  发生在编译期，所以不由 `Java` 虚拟机来执行
3.  应用场景： 方法重载（`OverLoad`）

因为在分派调用的时候，使用哪个重载版本完全取决于传入参数的数量和数据类型。而且，**虚拟机（准确说是编译期）在重载时是通过参数的静态类型而不是实际类型作为判断依据**，并且静态类型是编译期可知的。

重载是静态分派，会根据变量的**静态类型** 进行方法分派的行为。因此，上述代码中三个参数变量全部传入的是Human这个静态类型，也就都只会选择形参类型为Human去之执行。

#### 重写

```java
public class Dispatch {
    public static void main(String[] args) {
        Human human = new Human();
        sayHello(human);

        Human woman = new Woman();
        sayHello(woman);

        Human man = new Man();
        sayHello(man);
    }
    //重写是动态分配
    public  static void sayHello(Human human){
        human.sayHello();
    }
}

/*output: 
Hello, Human
Hello, Woman
Helllo, Man
*/
```

重写，指的是子类对父类方法的重新定义，但是**子类方法的方法名，参数列表和返回值类型，必须与父类方法一致！**所以可以简单的理解，重写就是子类对父类方法的核心进行重新定义。

#### 动态分派

根据变量的动态类型进行方法分派的行为，即根据变量的动态类型确定执行哪个方法，发生在运行期。

应用场景：方法重写（`Override`）

重写是动态分派，发生在运行期，会根据变量的**实际类型** 进行方法分派的行为。因此，上述代码中三个参数变量传入的是分别是Human,Woman,Man类型，会选择相应的函数去执行。

#### 总结

**重载**的本质是**在编译期就会根据参数的静态类型来决定重载方法的版本**，而**重写**的本质**在运行期确定接收者的实际类型**。

![image-20200731111417590](C:\Users\xuejing_hou\AppData\Roaming\Typora\typora-user-images\image-20200731111417590.png)

> 编译器在***重载***时是通过***参数的静态类型***而不是实际类型作为判定的依据。并且静态类型在编译期可知，因此，编译阶段，Javac编译器会根据**参数的静态类型**决定使用哪个重载版本。
>
> 所有依赖静态类型来定位方法执行版本的分派动作称为**静态分派**。静态分派的典型应用就是**方法重载**。
>
> 静态分派发生在编译阶段，因此确定静态分派的动作实际上不是由虚拟机来执行的，而是由编译器来完成。

> Java虚拟机是如何根据实际类型来分派方法执行版本的呢？
>  我们从invokevirtual指令的多态查找过程开始说起，***invokevirtual指令***的运行时解析过程大致分为以下几个步骤：
>
>  1、 找到操作数栈顶的第一个元素所指向的对象的**实际类型**，记作***C***。
>  2、 如果在**类型C**中找到与常量中的描述符和简单名称相符合的方法，然后进行访问权限验证，如果验证通过则返回这个方法的直接引用，查找过程结束；如果验证不通过，则抛出java.lang.IllegalAccessError异常。
>  3、 否则未找到，就按照继承关系**从下往上**依次对***类型C***的各个**父类**进行第2步的搜索和验证过程。
>  4、 如果始终没有找到合适的方法，则跑出java.lang.AbstractMethodError异常。
>
> 由于invokevirtual指令执行的第一步就是在运行期确定接收者的***实际类型***，所以两次调用中的invokevirtual指令把常量池中的***类方法符号引用***解析到了***不同***的***直接引用***上，这个过程就是Java语言方法***重写***的本质。我们把这种在运行期根据实际类型确定方法执行版本的分派过程称为***动态分派***。
>
> 但是，虚拟机”具体是如何做到的“，可能各种虚拟机实现都会有些差别?
>
> 由于动态分派是非常频繁的动作，而且动态分派的方法版本选择过程需要运行时在类的方法元数据中搜索合适的目标方法，因此虚拟机的实际实现中基于性能的考虑，大部分实现都不会真正的进行如此频繁的搜索。面对这种情况，最常用的***”稳定优化“***手段就是为类在方法区中建立一个虚方法表（Virtual Method Table，也称为vtable），使用***虚方法表索引***来***代替***元数据***查找***以提高性能。
>
> 
>
> ![img](https://img-blog.csdn.net/20160823200041852?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
>
> 
>
> 虚方法表中存放着各个方法的***实际入口地址***。如果某个方法在子类中没有被重写，那子类的虚方法表里面的地址入口和父类相同方法的地址入口是一致的，都是指向父类的实际入口。如果子类中重写了这个方法，子类方法表中的地址将会替换为指向子类实际版本的入口地址。
>
> 为了程序实现上的方便，具有相同签名的方法，在父类、子类的虚方法表中具有**一样的索引序号**，这样当类型变换时，仅仅需要变更查找的方法表，就可以从不同的虚方法表中按索引转换出所需要的入口地址。
>
> 方法表一般在类加载阶段的***连接阶段***进行初始化，准备了类的变量初始值后，虚拟机会把该类的方法表也初始化完毕。

### 参考链接

[Java的向上转型与向下转型](https://zhuanlan.zhihu.com/p/34026164)

[Java静态分派与动态分派](https://blog.csdn.net/sunxianghuang/article/details/52280002)

[**（JVM）Java虚拟机：静态分派 & 动态分派 原理解析**](https://www.jianshu.com/p/3ae6efff1c96)

[**Java 中的继承和多态（深入版）**](https://baijiahao.baidu.com/s?id=1662112966001886067&wfr=spider&for=pc)