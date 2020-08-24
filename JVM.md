# Java内存区域

### JVM 的主要组成部分及其作用

![img](https://img-blog.csdnimg.cn/20200103213149526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly90aGlua3dvbi5ibG9nLmNzZG4ubmV0,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020031416414486.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoaW5rV29u,size_16,color_FFFFFF,t_70)

JVM包含两个子系统和两个组件

* 两个子系统
  * Class loader(类装载器)
  * Execution engine(执行引擎)
* 两个组件
  * Native Interface(本地接口)
  * Runtime data area(运行时数据区)
    * 程序计数器（Program Counter Register）
      * 当前线程所执行的字节码的行号指示器，字节码解析器的工作是通过改变这个计数器的值，来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能，都需要依赖这个计数器来完成；
    * Java 虚拟机栈（Java Virtual Machine Stacks）
      * 用于存储局部变量表、操作数栈、动态链接、方法出口等信息；
    * 本地方法栈（Native Method Stack）
      * 与虚拟机栈的作用是一样的，只不过虚拟机栈是服务 Java 方法的，而本地方法栈是为虚拟机调用 Native 方法服务的；
    * Java 堆（Java Heap）
      * Java 虚拟机中内存最大的一块，是被所有线程共享的，几乎所有的对象实例都在这里分配内存；
    * 方法区（Methed Area）
      * 用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码等数据。

- Class loader(类装载)：根据给定的全限定名类名(如：java.lang.Object)来装载class文件到Runtime data area中的method area。
- Execution engine（执行引擎）：执行classes中的指令。
- Native Interface(本地接口)：与native libraries交互，是其它编程语言交互的接口。
- Runtime data area(运行时数据区域)：这就是我们常说的JVM的内存。

**作用** ：首先通过编译器把 Java 代码转换成字节码，类加载器（ClassLoader）再把字节码加载到内存中，将其放在运行时数据区（Runtime data area）的方法区内，而字节码文件只是 JVM 的一套指令集规范，并不能直接交给底层操作系统去执行，因此需要特定的命令解析器执行引擎（Execution Engine），将字节码翻译成底层系统指令，再交由 CPU 去执行，而这个过程中需要调用其他语言的本地库接口（Native Interface）来实现整个程序的功能。

### 深拷贝和浅拷贝

浅拷贝（shallowCopy）只是增加了一个指针指向已存在的内存地址，

深拷贝（deepCopy）是增加了一个指针并且申请了一个新的内存，使这个增加的指针指向这个新的内存，

使用深拷贝的情况下，释放内存的时候不会因为出现浅拷贝时释放同一个内存的错误。

浅复制：仅仅是指向被复制的内存地址，如果原地址发生改变，那么浅复制出来的对象也会相应的改变。

深复制：在计算机中开辟一块**新的内存地址**用于存放复制的对象。

### 说一下堆栈的区别

|              | 堆                                                           | 栈                                                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 物理地址     | 堆的物理地址分配对对象是不连续的。因此性能慢些。在GC的时候也要考虑到不连续的分配，所以有各种算法。比如，标记-消除，复制，标记-压缩，分代（即新生代使用复制算法，老年代使用标记——压缩） | 数据结构中的栈，先进后出的原则，物理地址分配是连续的。所以性能快。 |
| 内存分别     | 为是不连续的，所以分配的内存是在`运行期`确认的，因此大小不固定。一般堆大小远远大于栈 | 栈是连续的，所以分配的内存大小要在`编译期`就确认，大小是固定的。 |
| 存放的内容   | 存放的是对象的实例和数组。因此该区更关注的是数据的存储       | 存放：局部变量，操作数栈，返回结果。该区更关注的是程序方法的执行。 |
| 程序的可见度 | 堆对于整个应用程序都是共享、可见的                           | 栈只对于线程是可见的。所以也是线程私有。他的生命周期和线程相同。 |

|            | 队列                                       | 栈                                                 |
| ---------- | ------------------------------------------ | -------------------------------------------------- |
| 操作的名称 | 队列的插入称为入队，队列的删除称为出队     | 栈的插入称为进栈，栈的删除称为出栈                 |
| 操作的方式 | 队列是在队尾入队，队头出队，即两边都可操作 | 进栈和出栈都是在栈顶进行的，无法对栈底直接进行操作 |
| 操作的方法 | 队列是先进先出（FIFO）                     | 栈为后进先出（LIFO）                               |

# 内存溢出异常

## Java会存在内存泄漏吗

内存泄漏是指不再被使用的对象或者变量一直被占据在内存中。理论上来说，Java是有GC垃圾回收机制的，也就是说，不再被使用的对象，会被GC自动回收掉，自动从内存中清除。

但是，即使这样，Java也还是存在着内存泄漏的情况，java导致内存泄露的原因很明确：长生命周期的对象持有短生命周期对象的引用就很可能发生内存泄露，尽管短生命周期对象已经不再需要，但是因为长生命周期对象持有它的引用而导致不能被回收，这就是java中内存泄露的发生场景。

# 垃圾收集器

### 简述Java垃圾回收

在java中，程序员是不需要显示的去释放一个对象的内存的，而是由虚拟机自行执行。在JVM中，有一个垃圾回收线程，它是低优先级的，在正常情况下是不会执行的，只有在虚拟机空闲或者当前堆内存不足时，才会触发执行，扫面那些没有被任何引用的对象，并将它们添加到要回收的集合中，进行回收。

### GC

GC 是垃圾收集的意思（Gabage Collection）,内存处理是编程人员容易出现问题的地方，忘记或者错误的内存

回收会导致程序或系统的不稳定甚至崩溃，Java 提供的 GC 功能可以自动监测对象是否超过作用域从而达到自动

回收内存的目的，Java 语言没有提供释放已分配内存的显示操作方法。

### 原理

垃圾回收器通常作为一个单独的低级别的线程运行，在不可预知的情况下对内存堆中已经死亡的或很长时间没有用过的对象进行清除和回收。

垃圾回收有分代复制垃圾回收、标记垃圾回收、增量垃圾回收。

对于GC来说，创建对象时，GC就开始监控这个对象的地址、大小以及使用情况。

通常，GC采用有向图的方式记录和管理堆(heap)中的所有对象。通过这种方式确定哪些对象是"可达的"，哪些对象是"不可达的"。当GC确定一些对象为"不可达"时，GC就有责任回收这些内存空间。

















```java
public class Demo {
    public static void main(String[] args) {
        System.out.print("Hello world");
    }
}
```

Java栈报错`java.lang.StackOverflowError`
打印的结果是最后一次调用

```java
public class Demo2 {
    private static int length = 0;

    public static void recursion() {
        length++;
        recursion();
    }

    public static void main(String[] args) {
        try {
            recursion();
        } catch (Throwable ex) {
            ex.printStackTrace();
            System.out.println(length);
        }
    }
}
```

### 忘记是啥功能了

```shell
ps -mp pid -o THREAD,tid,time
```

### CPU占用率高查看

#### 第一种方法

```shell
# 1.查看java的pid
jps

# 2.查看各个子进程
top -H -p pid

# 3.显示16进制
printf "%x\n" 子pid

jstack pid |grep tid(第3步出来的) -A 30
```

#### 第二种方法:show-busy-java-threads

https://github.com/oldratlee/useful-scripts/blob/dev-2.x/bin/show-busy-java-threads

使用的命令:

```shell
sh show-busy-java-threads.sh -p pid
```



