# 1.Continue 和 Break



# **2.List转成string,去掉两边的中括号**

```java
StringUtils.strip(partnerApplyDetail1.getList().toString().toString(),"[]");
```

# 3.Long类型的比较

```java
public static void main(String[] args) {
    Long long1 = new Long(120);
    Long long2 = new Long(120);
    System.out.println(long1 == long2);  //false

    long long3 = 120;
    long long4 = 120;
    System.out.println(long3 == long4);  //true
}
```

# 基础

## IO

## 运算符





# 知识点

## equals()

### ==

对于基本类型和引用类型 == 的作用效果是不同的，如下所示：

- 基本类型：比较的是值是否相同；
- 引用类型：比较的是引用是否相同；

```java
String x = "string";
String y = "string";
String z = new String("string");
System.out.println(x==y); // true
System.out.println(x==z); // false
System.out.println(x.equals(y)); // true
System.out.println(x.equals(z)); // true
```

代码解读：因为 x 和 y 指向的是同一个引用，所以 == 也是 true，而 new String()方法则重写开辟了内存空间，所以 == 结果为 false，而 equals 比较的一直是值，所以结果都为 true。

### equals

equals 本质上就是 ==，只不过 String 和 Integer 等重写了 equals 方法，把它变成了值比较。看下面的代码就明白了。

首先来看默认情况下 equals 比较一个有相同值的对象，代码如下：

```java
class Cat {
    public Cat(String name) {
        this.name = name;
    }

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

Cat c1 = new Cat("王磊");
Cat c2 = new Cat("王磊");
System.out.println(c1.equals(c2)); // false
System.out.println(c1 == c2); // false
```

输出结果出乎我们的意料，竟然是 false？这是怎么回事，看了 equals 源码就知道了，源码如下：

```java
public boolean equals(Object obj) {
		return (this == obj);
}
```

`原来 equals 本质上就是 ==。`

`那问题来了，两个相同值的 String 对象，为什么返回的是 true？代码如下：`

```java
String s1 = new String("老王");
String s2 = new String("老王");
System.out.println(s1.equals(s2)); // true

public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

原来是 String 重写了 Object 的 equals 方法，把引用比较改成了值比较。

总体来说，== 对于基本类型来说是值比较，对于引用类型来说是比较的是引用；而 equals 默认情况下是引用比较，只是很多类重写了 equals 方法，比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。

## equals的重写规则

```java
public class Car {
	private int batch;
	public Car(int batch) {
		this.batch = batch;
	}
	public static void main(String[] args) {
		Car c1 = new Car(1);
		Car c2 = new Car(1);
		Car c3 = new Car(1);
		System.out.println("自反性->c1.equals(c1)：" + c1.equals(c1));
		System.out.println("对称性：");
		System.out.println(c1.equals(c2));
		System.out.println(c2.equals(c1));
		System.out.println("传递性：");
		System.out.println(c1.equals(c2));
		System.out.println(c2.equals(c3));
		System.out.println(c1.equals(c3));
		System.out.println("一致性：");
		for (int i = 0; i < 50; i++) {
			if (c1.equals(c2) != c1.equals(c2)) {
				System.out.println("equals方法没有遵守一致性！");
				break;
			}
		}
		System.out.println("equals方法遵守一致性！");
		System.out.println("与null比较：");
		System.out.println(c1.equals(null));
	}
	@Override
	public boolean equals(Object obj) {
		if (obj instanceof Car) {
			Car c = (Car) obj;
			return batch == c.batch;
		}
		return false;
	}
}
```

##  重写equals()的同时还得重写hashCode()

要是针对映射相关的操作（Map接口）。

Map接口的类会使用到键对象的哈希码，当我们调用put方法或者get方法对Map容器进行操作时，都是根据键对象的哈希码来计算存储位置的，因此如果我们对哈希码的获取没有相关保证，就可能会得不到预期的结果

```java
public class HashCodeTest {
	public static void main(String[] args) {
		String s="ok";
		StringBuilder sb =new StringBuilder(s);		
		System.out.println(s.hashCode()+"  "+sb.hashCode());
		
		String t = new String("ok");
		StringBuilder tb =new StringBuilder(s);
		System.out.println(t.hashCode()+"  "+tb.hashCode());
	}
}

# 运行结果
3548  1829164700
3548  2018699554
```

jdk中的`hashCode()`源码

```java
# object的方法
public native int hashCode();

# objects的方法
public static int hashCode(Object o) {
       return o != null ? o.hashCode() : 0;
}
```

重写hashCode()

```java
public class Model {

    private   String name;
    private double salary;
    private int sex;

    public Model(String name, double salary, int sex) {
        this.name = name;
        this.salary = salary;
        this.sex = sex;
    }

    @Override
    public int hashCode() {
        return Objects.hashCode(name)+new Double(salary).hashCode()+ new Integer(sex).hashCode();
    }

    public static void main(String[] args) {
        Model anthony = new Model("anthony", 23, 1);
        Model anthony2 = new Model("anthony", 23, 1);
        System.out.println(anthony.hashCode());
        System.out.println(anthony2.hashCode());
    }
}

# 输出结果
230433658
230433658
```

重写hadcode在map中的应用

```java
import java.util.HashMap;
import java.util.Map;
public class MapTest {
    public static void main(String[] args) {
        Map<String,Value> map1 = new HashMap<String,Value>();
        String s1 = new String("key");
        String s2 = new String("key");
        Value value = new Value(2);
        map1.put(s1, value);
        System.out.println("s1.equals(s2):"+s1.equals(s2));
        System.out.println("map1.get(s1):"+map1.get(s1));
        System.out.println("map1.get(s2):"+map1.get(s2));


        Map<Key,Value> map2 = new HashMap<Key,Value>();
        Key k1 = new Key("A");
        Key k2 = new Key("A");
        map2.put(k1, value);
        System.out.println("k1.equals(k2):"+s1.equals(s2));
        System.out.println("map2.get(k1):"+map2.get(k1));
        System.out.println("map2.get(k2):"+map2.get(k2));
    }

    static class Key{
        private String k;
        public Key(String key){
            this.k=key;
        }

        @Override
        public boolean equals(Object obj) {
            if(obj instanceof Key){
                Key key=(Key)obj;
                return k.equals(key.k);
            }
            return false;
        }

        /**
         *
         * 没有重写hashcode方法,结果是:
         * s1.equals(s2):true
         * map1.get(s1):类Value的值－－>2
         * map1.get(s2):类Value的值－－>2
         * k1.equals(k2):true
         * map2.get(k1):类Value的值－－>2
         * map2.get(k2):类Value的值－－>null
         *
         *
         * 重写了hadcode方法,结果是:
         * s1.equals(s2):true
         * map1.get(s1):类Value的值－－>2
         * map1.get(s2):类Value的值－－>2
         * k1.equals(k2):true
         * map2.get(k1):类Value的值－－>2
         * map2.get(k2):类Value的值－－>2
         */
        @Override
        public int hashCode() {
            return k.hashCode();
        }
    }

    static class Value{
        private int v;

        public Value(int v){
            this.v=v;
        }

        @Override
        public String toString() {
            return "类Value的值－－>"+v;
        }
    }
}
```

## 基本类型包装类对象常量池

（1）只有使用`引号`包含文本的方式创建的String对象之间使用“+”连接产生的新对象才会被加入字符串池中。
 （2）对于所有包含new方式新建对象（包括null）的“+”连接表达式，它所产生的新对象都不会被加入字符串池中。

```java
public class StringDemo {
    public static void main(String[] args) {
        String str1 = "str";
        String str2 = "ing";

        String str3 = "str" + "ing";
        // 在编译器中实际是创建了StringBuilder,犹如下面的例子
        String str4 = str1 + str2;
        System.out.println(str3 == str4);//false
        // 例子
        StringBuilder stringBuilder = new StringBuilder();
        stringBuilder.append("str");
        stringBuilder.append("ing");
        System.out.println(str3==stringBuilder.toString());//false

        String str5 = "string";
        System.out.println(str3 == str5);//true
    }
}
# 所以s4没有使用的引号,才是false
```

`String str4 = str1 + str2;`的反编译

```java
C:\lzkj-item\api-agent\src\main\java>javap -c StringDemo  
Compiled from "StringDemo.java"   
public class StringDemo {         
  public StringDemo();
    Code: 
       0: aload_0     
       1: invokespecial #1      // Method java/lang/Object."<init>":()V           
       4: return      
          
  public static void main(java.lang.String[]);
    Code:    
       0: ldc           #2      // String str 
       2: astore_1    
       3: ldc           #3      // String ing 
       5: astore_2    
       6: ldc           #4      // String string          
       8: astore_3    
       9: new           #5      // class java/lang/StringBuilder      
      12: dup         
      13: invokespecial #6      // Method java/lang/StringBuilder."<init>":()V    
      16: aload_1     
      17: invokevirtual #7      // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      20: aload_2     
      21: invokevirtual #7      // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      24: invokevirtual #8      // Method java/lang/StringBuilder.toString:()Ljava/lang/String;           
      27: astore        4         
      29: getstatic     #9      // Field java/lang/System.out:Ljava/io/PrintStream;           
      32: aload_3     
      33: aload         4         
      35: if_acmpne     42        
      38: iconst_1    
      39: goto          43        
      42: iconst_0    
      43: invokevirtual #10     // Method java/io/PrintStream.println:(Z)V        
      46: return      
}
```

特例:

```java
public static final String A = "ab"; // 常量A
public static final String B = "cd"; // 常量B
public static void main(String[] args) {
     String s = A + B;  // 将两个常量用+连接对s进行初始化 
     String t = "abcd";   
    if (s == t) {   
         System.out.println("s等于t，它们是同一个对象");   
     } else {   
         System.out.println("s不等于t，它们不是同一个对象");   
     }   
 } 
# s等于t，它们是同一个对象
# A和B都是常量，值是固定的，因此s的值也是固定的，它在类被编译时就已经确定了。也就是说：String s=A+B; 等同于：String s="ab"+"cd";

C:\lzkj-item\api-agent\src\main\java>javap -c StringDemo   
Compiled from "StringDemo.java"      
public class StringDemo { 
  public static final java.lang.String A;       
    
  public static final java.lang.String B;       
    
  public StringDemo();    
    Code:      
       0: aload_0         
       1: invokespecial #1       // Method java/lang/Object."<init>":()V         
       4: return          
    
  public static void main(java.lang.String[]);  
    Code:      
       0: ldc#3       // String abcd 
       2: astore_1        
       3: ldc#3       // String abcd 
       5: astore_2        
       6: aload_1         
       7: aload_2         
       8: if_acmpne     22
      11: getstatic     #4       // Field java/lang/System.out:Ljava/io/PrintStream;        
      14: ldc#5       // String s等于t，它们是同一个对象   
      16: invokevirtual #6       // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      19: goto          30
      22: getstatic     #4       // Field java/lang/System.out:Ljava/io/PrintStream;        
      25: ldc#7       // String s不等于t，它们不是同一个对象 
      27: invokevirtual #6       // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      30: return          
}
# 第30行代码的abcd,指的是s
# 第32行代码的abcd,指的是t

# ----------------------------------------------------------------------------------------------

public static final String A; // 常量A
public static final String B;    // 常量B
static {   
     A = "ab";   
     B = "cd";   
 }   
 public static void main(String[] args) {   
    // 将两个常量用+连接对s进行初始化   
     String s = A + B;   
     String t = "abcd";   
    if (s == t) {   
         System.out.println("s等于t，它们是同一个对象");   
     } else {   
         System.out.println("s不等于t，它们不是同一个对象");   
     }   
 } 
# s不等于t，它们不是同一个对象
# A和B虽然被定义为常量，但是它们都没有马上被赋值。在运算出s的值之前，他们何时被赋值，以及被赋予什么样的值，都是个变数。因此A和B在被赋值之前，性质类似于一个变量。那么s就不能在编译期被确定，而只能在运行时被创建了。
    
C:\lzkj-item\api-agent\src\main\java>javap -c StringDemo
Compiled from "StringDemo.java"
public class StringDemo {
  public static final java.lang.String A;

  public static final java.lang.String B;

  public StringDemo();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class java/lang/StringBuilder
       3: dup
       4: invokespecial #3                  // Method java/lang/StringBuilder."<init>":()V
       7: getstatic     #4                  // Field A:Ljava/lang/String;
      10: invokevirtual #5                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      13: getstatic     #6                  // Field B:Ljava/lang/String;
      16: invokevirtual #5                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      19: invokevirtual #7                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      22: astore_1
      23: ldc           #8                  // String abcd
      25: astore_2
      26: aload_1
      27: aload_2
      28: if_acmpne     42
      31: getstatic     #9                  // Field java/lang/System.out:Ljava/io/PrintStream;
      34: ldc           #10                 // String s等于t，它们是同一个对象
      36: invokevirtual #11                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      39: goto          50
      42: getstatic     #9                  // Field java/lang/System.out:Ljava/io/PrintStream;
      45: ldc           #12                 // String s不等于t，它们不是同一个对象
      47: invokevirtual #11                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      50: return

  static {};
    Code:
       0: ldc           #13                 // String ab
       2: putstatic     #4                  // Field A:Ljava/lang/String;
       5: ldc           #14                 // String cd
       7: putstatic     #6                  // Field B:Ljava/lang/String;
      10: return
}

# 第94的abcd指的是t
```

## 字符串常量池

### String Pool机制

目的:JVM为了提高性能和减少内存开销,实现数据共享

1.为字符串开辟一个字符串常量池(存在于方法区)

2.创建字符串常量时,首页坚持字符串常量池是否存在该字符串

3.存在该字符串,返回引用实例,不存在,实例化该字符串并放入池中

### intern()

String的intern()方法会查找在常量池中是否存在一份equal相等的字符串,如果有则返回该字符串的引用,如果没有则添加自己的字符串进入常量池

```java
public static void main(String[] args) {    
      String s1 = new String("计算机");
      String s2 = s1.intern();
      String s3 = "计算机";
      System.out.println("s1 == s2? " + (s1 == s2));
      System.out.println("s3 == s2? " + (s3 == s2));
}
s1 == s2? false
s3 == s2? true
```

## Integer常量池

```java
public class ArrayTest {
    public static void main(String[] args) {
        Integer i1 = new Integer(1);
        Integer i2 = new Integer(1);
        System.out.println(i1.hashCode()); // 1
        System.out.println(i2.hashCode()); // 1
        System.out.println(i1 == i2);      // false,new出来的地址不一样
        System.out.println(i1.equals(i2)); // true,数值内容是一样的

        System.out.println("-------------------");

        Integer i3 = 1;
        Integer i4 = 1;
        System.out.println(i3.hashCode()); // 1
        System.out.println(i4.hashCode()); // 1
        System.out.println(i3 == i4);  	   // true,在常量池汇中,应用一样	
        System.out.println(i3.equals(i4)); // true,数值内容一样
    }
}
```



```java
# 源码
public class ArrayTest {
    public static void main(String[] args) {
        Integer i1 = 25;
        Integer i2 = new Integer(26);
    }
}

# 反编译
public class ArrayTest{
  public static void main(String[] paramArrayOfString){
    Integer localInteger1 = Integer.valueOf(25);
    Integer localInteger2 = new Integer(26);
  }
}

# valueOf源码
public static Integer valueOf(int i) {
    assert IntegerCache.high >= 127;
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

## Class常量池

###  class常量池简介(Class Constant Pool)

我们写的每一个Java类被编译后，就会形成一份class文件；class文件中除了包含类的版本、字段、方法、接口等描述信息外，还有一项信息就是常量池(constant pool table)，用于存放编译器生成的各种字面量(Literal)和符号引用(Symbolic References)；
每个class文件都有一个class常量池

###  什么是字面量和符号引用

- 字面量包括：1.文本字符串 2.八种基本类型的值 3.被声明为final的常量等;
- 符号引用包括：1.类和方法的全限定名 2.字段的名称和描述符 3.方法的名称和描述符

## 运行时常量池(Runtime Constant Pool)

运行时常量池存在于内存中，也就是class常量池被加载到内存之后的版本，不同之处是：它的字面量可以动态的添加(String#intern()),符号引用可以被解析为直接引用
JVM在执行某个类的时候，必须经过加载、连接、初始化，而连接又包括验证、准备、解析三个阶段。而当类加载到内存中后，jvm就会将class常量池中的内容存放到运行时常量池中，由此可知，运行时常量池也是每个类都有一个。在解析阶段，会把符号引用替换为直接引用，解析的过程会去查询字符串常量池，也就是我们上面所说的StringTable，以保证运行时常量池所引用的字符串与字符串常量池中是一致的

## 集合框架

![](https://image.yanganlin.com/blog/20200717134605.jpg)

### HashMap源码

![img](https://raw.githubusercontent.com/YangAnLin/images/master/20200831115117.webp)

#### put方法具体实现







# 并发

## Java 中用到的线程调度算法是什么

JAVA 虚拟机的一项任务就是负责线程的调度，线程调度是指按照特定机制为多个线程分配 CPU 的使用权。

有两种调度模型：`分时调度模型`和`抢占式调度模型`

分时调度模型是指让所有的线程轮流获得 cpu 的使用权，并且平均分配每个线程占用的 CPU 的时间片这个也比较好理解。

Java虚拟机采用抢占式调度模型，是指优先让可运行池中优先级高的线程占用CPU，如果可运行池中的线程优先级相同，那么就随机选择一个线程，使其占用CPU。处于运行状态的线程会一直运行，直至它不得不放弃 CPU。

## runnable 和 callable 有什么区别

- Runnable 接口 run 方法无返回值；Callable 接口 call 方法有返回值，是个泛型，和Future、FutureTask配合可以用来获取异步执行的结果
- Runnable 接口 run 方法只能抛出运行时异常，且无法捕获处理；Callable 接口 call 方法允许抛出异常，可以获取异常信息

**注**：Callalbe接口支持返回执行结果，需要调用FutureTask.get()得到，此方法会阻塞主进程的继续往下执行，如果不调用不会阻塞。

## 线程的 run()和 start()有什么区别

## start() 方法时会执行 run() 方法

new 一个 Thread，线程进入了新建状态。调用 start() 方法，会启动一个线程并使线程进入了就绪状态，当分配到时间片后就可以开始运行了。 start() 会执行线程的相应准备工作，然后自动执行 run() 方法的内容，这是真正的多线程工作。

而直接执行 run() 方法，会把 run 方法当成一个 main 线程下的普通方法去执行，并不会在某个线程中执行它，所以这并不是多线程工作。

总结： 调用 start 方法方可启动线程并使线程进入就绪状态，而 run 方法只是 thread 的一个普通方法调用，还是在主线程里执行。

## 什么是 FutureTask

FutureTask 表示一个异步运算的任务。FutureTask 里面可以传入一个 Callable 的具体实现类，可以对这个异步运算的任务的结果进行等待获取、判断是否已经完成、取消任务等操作。只有当运算完成的时候结果才能取回，如果运算尚未完成 get 方法将会阻塞。一个 FutureTask 对象可以对调用了 Callable 和 Runnable 的对象进行包装，由于 FutureTask 也是Runnable 接口的实现类，所以 FutureTask 也可以放入线程池中。

## 线程的 sleep()方法和 yield()方法有什么区别？

（1） sleep()方法给其他线程运行机会时不考虑线程的优先级，因此会给低优先级的线程以运行的机会；yield()方法只会给相同优先级或更高优先级的线程以运行的机会；

（2） 线程执行 sleep()方法后转入阻塞（blocked）状态，而执行 yield()方法后转入就绪（ready）状态；

（3）sleep()方法声明抛出 InterruptedException，而 yield()方法没有声明任何异常；

（4）sleep()方法比 yield()方法（跟操作系统 CPU 调度相关）具有更好的可移植性，通常不建议使用yield()方法来控制并发线程的执行。

## 如何停止一个正在运行的线程？

1. 使用退出标志，使线程正常退出，也就是当run方法完成后线程终止。
2. 使用stop方法强行终止，但是不推荐这个方法，因为stop和suspend及resume一样都是过期作废的方法。
3. 使用interrupt方法中断线程。

## 如果你提交任务时，线程池队列已满，这时会发生什么

这里区分一下：

（1）如果使用的是无界队列 LinkedBlockingQueue，也就是无界队列的话，没关系，继续添加任务到阻塞队列中等待执行，因为 LinkedBlockingQueue 可以近乎认为是一个无穷大的队列，可以无限存放任务

（2）如果使用的是有界队列比如 ArrayBlockingQueue，任务首先会被添加到ArrayBlockingQueue 中，ArrayBlockingQueue 满了，会根据maximumPoolSize 的值增加线程数量，如果增加了线程数量还是处理不过来，ArrayBlockingQueue 继续满，那么则会使用拒绝策略RejectedExecutionHandler 处理满了的任务，默认是 AbortPolicy

## 什么叫线程安全？servlet 是线程安全吗?

线程安全是编程中的术语，指某个方法在多线程环境中被调用时，能够正确地处理多个线程之间的共享变量，使程序功能正确完成。

Servlet 不是线程安全的，servlet 是单实例多线程的，当多个线程同时访问同一个方法，是不能保证共享变量的线程安全性的。

Struts2 的 action 是多实例多线程的，是线程安全的，每个请求过来都会 new 一个新的 action 分配给这个请求，请求完成后销毁。

SpringMVC 的 Controller 是线程安全的吗？不是的，和 Servlet 类似的处理流程。

Struts2 好处是不用考虑线程安全问题；Servlet 和 SpringMVC 需要考虑线程安全问题，但是性能可以提升不用处理太多的 gc，可以使用 ThreadLocal 来处理多线程的问题。

## 线程类的构造方法、静态块是被哪个线程调用的

线程类的构造方法、静态块是被 new这个线程类所在的线程所调用的，而 run 方法里面的代码才是被线程自身所调用的。

举个例子，假设 Thread2 中 new 了Thread1，main 函数中 new 了 Thread2，那么：

（1）Thread2 的构造方法、静态块是 main 线程调用的，Thread2 的 run()方法是Thread2 自己调用的

（2）Thread1 的构造方法、静态块是 Thread2 调用的，Thread1 的 run()方法是Thread1 自己调用的

## 为什么代码会重排序？

在执行程序时，为了提供性能，处理器和编译器常常会对指令进行重排序，但是不能随意重排序，不是你想怎么排序就怎么排序，它需要满足以下两个条件：

- 在单线程环境下不能改变程序运行的结果；
- 存在数据依赖关系的不允许重排序

需要注意的是：重排序不会影响单线程环境的执行结果，但是会破坏多线程的执行语义。

## ThreadLocal

### ThreadLocal 是什么？有哪些使用场景？

ThreadLocal 是一个本地线程副本变量工具类，在每个线程中都创建了一个 ThreadLocalMap 对象，简单说 ThreadLocal 就是一种以空间换时间的做法，每个线程可以访问自己内部 ThreadLocalMap 对象内的 value。通过这种方式，避免资源在多线程间共享。

原理：线程局部变量是局限于线程内部的变量，属于线程自身所有，不在多个线程间共享。Java提供ThreadLocal类来支持线程局部变量，是一种实现线程安全的方式。但是在管理环境下（如 web 服务器）使用线程局部变量的时候要特别小心，在这种情况下，工作线程的生命周期比任何应用变量的生命周期都要长。任何线程局部变量一旦在工作完成后没有释放，Java 应用就存在内存泄露的风险。

经典的使用场景是为每个线程分配一个 JDBC 连接 Connection。这样就可以保证每个线程的都在各自的 Connection 上进行数据库的操作，不会出现 A 线程关了 B线程正在使用的 Connection； 还有 Session 管理 等问题。

### ThreadLocal造成内存泄漏的原因？

`ThreadLocalMap` 中使用的 key 为 `ThreadLocal` 的弱引用,而 value 是强引用。所以，如果 `ThreadLocal` 没有被外部强引用的情况下，在垃圾回收的时候，key 会被清理掉，而 value 不会被清理掉。这样一来，`ThreadLocalMap` 中就会出现key为null的Entry。假如我们不做任何措施的话，value 永远无法被GC 回收，这个时候就可能会产生内存泄露。ThreadLocalMap实现中已经考虑了这种情况，在调用 `set()`、`get()`、`remove()` 方法的时候，会清理掉 key 为 null 的记录

### ThreadLocal内存泄漏解决方案？

- 每次使用完ThreadLocal，都调用它的remove()方法，清除数据。
- 在使用线程池的情况下，没有及时清理ThreadLocal，不仅是内存泄漏的问题，更严重的是可能导致业务逻辑出现问题

##  BlockingQueue

### 什么是阻塞队列

阻塞队列（BlockingQueue）是一个支持两个附加操作的队列。

这两个附加的操作是：

* 在队列为空时，获取元素的线程会等待队列变为非空。

* 当队列满时，存储元素的线程会等待队列可用。

阻塞队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。阻塞队列就是生产者存放元素的容器，而消费者也只从容器里拿元素。

BlockingQueue 接口是 Queue 的子接口，它的主要用途并不是作为容器，而是作为线程同步的的工具

JDK7 提供了 7 个阻塞队列。分别是：

ArrayBlockingQueue ：一个由数组结构组成的有界阻塞队列。

LinkedBlockingQueue ：一个由链表结构组成的有界阻塞队列。

PriorityBlockingQueue ：一个支持优先级排序的无界阻塞队列。

DelayQueue：一个使用优先级队列实现的无界阻塞队列。

SynchronousQueue：一个不存储元素的阻塞队列。

LinkedTransferQueue：一个由链表结构组成的无界阻塞队列。

LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列。

### ConcurrentLinkedQueue详解

### ArrayBlockingQueue详解

### LinkedBlockingQueue详解

## 线程池

### 创建线程池

线程池顾名思义就是事先创建若干个可执行的线程放入一个池（容器）中，需要的时候从池中获取线程不用自行创建，使用完毕不需要销毁线程而是放回池中，从而减少创建和销毁线程对象的开销

（1）**newSingleThreadExecutor**：创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行。

（2）**newFixedThreadPool**：创建固定大小的线程池。每次提交一个任务就创建一个线程，直到线程达到线程池的最大大小。线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。如果希望在服务器上使用线程池，建议使用 newFixedThreadPool方法来创建线程池，这样能获得更好的性能。

（3） newCachedThreadPool：创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60 秒不执行任务）的线程，当任务数增加时，此线程池又可以智能的添加新线程来处理任务。此线程池不会对线程池大小做限制，线程池大小完全依赖于操作系统（或者说 JVM）能够创建的最大线程大小。

（4）newScheduledThreadPool：创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求。

### 线程池都有哪些状态

- RUNNING：这是最正常的状态，接受新的任务，处理等待队列中的任务。
- SHUTDOWN：不接受新的任务提交，但是会继续处理等待队列中的任务。
- STOP：不接受新的任务提交，不再处理等待队列中的任务，中断正在执行任务的线程。
- TIDYING：所有的任务都销毁了，workCount 为 0，线程池的状态在转换为 TIDYING 状态时，会执行钩子方法 terminated()。
- TERMINATED：terminated()方法结束后，线程池的状态就会变成这个

### Executor 框架

Executor 框架是一个根据一组执行策略调用，调度，执行和控制的异步任务的框架。

每次执行任务创建线程 new Thread()比较消耗性能，创建一个线程是比较耗时、耗资源的，而且无限制的创建线程会引起应用程序内存溢出。

所以创建一个线程池是个更好的的解决方案，因为可以限制线程的数量并且可以回收再利用这些线程。利用Executors 框架可以非常方便的创建一个线程池。

### Executor 和 Executors 的区别

- Executors 工具类的不同方法按照我们的需求创建了不同的线程池，来满足业务的需求。
- Executor 接口对象能执行我们的线程任务。
- ExecutorService 接口继承了 Executor 接口并进行了扩展，提供了更多的方法我们能获得任务执行的状态并且可以获取任务的返回值。
- 使用 ThreadPoolExecutor 可以创建自定义线程池。
- Future 表示异步计算的结果，他提供了检查计算是否完成的方法，以等待计算的完成，并可以使用 get()方法获取计算的结果

### 线程池中 submit() 和 execute()

接收参数：execute()只能执行 Runnable 类型的任务。submit()可以执行 Runnable 和 Callable 类型的任务。

返回值：submit()方法可以返回持有计算结果的 Future 对象，而execute()没有

异常处理：submit()方便Exception处理

### ThreadPoolExecutor详解

![图解线程池实现原理](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOS8xMS8yNS8xNmVhMDQ3NjEyOTVlNzY2?x-oss-process=image/format,png)

#### Executors和ThreaPoolExecutor创建线程池的区别

《阿里巴巴Java开发手册》中强制线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险

Executors 各个方法的弊端：

- newFixedThreadPool 和 newSingleThreadExecutor:
  主要问题是堆积的请求处理队列可能会耗费非常大的内存，甚至 OOM。
- newCachedThreadPool 和 newScheduledThreadPool:
  主要问题是线程数最大数是 Integer.MAX_VALUE，可能会创建数量非常多的线程，甚至 OOM。

ThreaPoolExecutor创建线程池方式只有一种，就是走它的构造函数，参数自己指定

#### ThreadPoolExecutor构造函数重要参数分析

**`ThreadPoolExecutor`** **3 个最重要的参数：**

- **`corePoolSize`** ：核心线程数，线程数定义了最小可以同时运行的线程数量。
- **`maximumPoolSize`** ：线程池中允许存在的工作线程的最大数量
- **`workQueue`**：可以用前面的阻塞队列,当新任务来的时候会先判断当前运行的线程数量是否达到核心线程数，如果达到的话，任务就会被存放在队列中。

`ThreadPoolExecutor`其他常见参数:

1. **`keepAliveTime`**：线程池中的线程数量大于 `corePoolSize` 的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了 `keepAliveTime`才会被回收销毁；
2. **`unit`** ：`keepAliveTime` 参数的时间单位。
3. **`threadFactory`**：为线程池提供创建新线程的线程工厂
4. **`handler`** ：线程池任务队列超过 maxinumPoolSize 之后的拒绝策略

#### ThreadPoolExecutor饱和策略

**`ThreadPoolExecutor`** **饱和策略定义:**

如果当前同时运行的线程数量达到最大线程数量并且队列也已经被放满了任时，`ThreadPoolTaskExecutor` 定义一些策略:

- **`ThreadPoolExecutor.AbortPolicy`**：抛出 `RejectedExecutionException`来拒绝新任务的处理。
- **`ThreadPoolExecutor.CallerRunsPolicy`**：调用执行自己的线程运行任务。您不会任务请求。但是这种策略会降低对于新任务提交速度，影响程序的整体性能。另外，这个策略喜欢增加队列容量。如果您的应用程序可以承受此延迟并且你不能任务丢弃任何一个任务请求的话，你可以选择这个策略。
- **`ThreadPoolExecutor.DiscardPolicy`**：不处理新任务，直接丢弃掉。
- **`ThreadPoolExecutor.DiscardOldestPolicy`**： 此策略将丢弃最早的未处理的任务请求。

举个例子： Spring 通过 `ThreadPoolTaskExecutor` 或者我们直接通过 `ThreadPoolExecutor` 的构造函数创建线程池的时候，当我们不指定 `RejectedExecutionHandler` 饱和策略的话来配置线程池的时候默认使用的是 `ThreadPoolExecutor.AbortPolicy`。

在默认情况下，`ThreadPoolExecutor` 将抛出 `RejectedExecutionException` 来拒绝新来的任务 ，这代表你将丢失对这个任务的处理。

对于可伸缩的应用程序，建议使用 `ThreadPoolExecutor.CallerRunsPolicy`。当最大池被填满时，此策略为我们提供可伸缩队列。

### ScheduledThreadPoolExecutor详解

### ScheduledThreadPoolExecutor详解(没有)

### FutureTask详解(没有)

### 原子操作类

原子操作（atomic operation）意为”不可被中断的一个或一系列操作” 。

int++并不是一个原子操作，所以当一个线程读取它的值并加 1 时，另外一个线程有可能会读到之前的值，这就会引发错误。

java.util.concurrent 这个包里面提供了一组原子类。其基本的特性就是在多线程环境下，当有多个线程同时执行这些类的实例包含的方法时，具有排他性，即当某个线程进入方法，执行其中的指令时，不会被其他线程打断，而别的线程就像自旋锁一样，一直等到该方法执行完成，才由 JVM 从等待队列中选择另一个线程进入，这只是一种逻辑上的理解。

原子类：AtomicBoolean，AtomicInteger，AtomicLong，AtomicReference

原子数组：AtomicIntegerArray，AtomicLongArray，AtomicReferenceArray

原子属性更新器：AtomicLongFieldUpdater，AtomicIntegerFieldUpdater，AtomicReferenceFieldUpdater

解决 ABA 问题的原子类：AtomicMarkableReference（通过引入一个 boolean来反映中间有没有变过），AtomicStampedReference（通过引入一个 int 来累加来反映中间有没有变过）

#### Atomic 的原理 

Atomic包中的类基本的特性就是在多线程环境下，当有多个线程同时对单个（包括基本类型及引用类型）变量进行操作时，具有排他性，即当多个线程同时对该变量的值进行更新时，仅有一个线程能成功，而未成功的线程可以向自旋锁一样，继续尝试，一直等到执行成功。

```java
// setup to use Unsafe.compareAndSwapInt for updates（更新操作时提供“比较并替换”的作用）
private static final Unsafe unsafe = Unsafe.getUnsafe();
private static final long valueOffset;

static {
	try {
		valueOffset = unsafe.objectFieldOffset
		(AtomicInteger.class.getDeclaredField("value"));
	} catch (Exception ex) { throw new Error(ex); }
}

private volatile int value;
```

AtomicInteger 类主要利用 CAS (compare and swap) + volatile 和 native 方法来保证原子操作，从而避免 synchronized 的高开销，执行效率大为提升。

CAS的原理是拿期望的值和原本的一个值作比较，如果相同则更新成新的值。UnSafe 类的 objectFieldOffset() 方法是一个本地方法，这个方法是用来拿到“原来的值”的内存地址，返回值是 valueOffset。另外 value 是一个volatile变量，在内存中可见，因此 JVM 可以保证任何时刻任何线程总能拿到该变量的最新值。

## 并发工具

### CountDownLatch与CyclicBarrier

CountDownLatch与CyclicBarrier都是用于控制并发的工具类，都可以理解成维护的就是一个计数器，但是这两者还是各有不同侧重点的：

- CountDownLatch一般用于某个线程A等待若干个其他线程执行完任务之后，它才执行；
- CyclicBarrier一般用于一组线程互相等待至某个状态，然后这一组线程再同时执行；
- CountDownLatch强调一个线程等多个线程完成某件事情。
- CyclicBarrier是多个线程互等，等大家都完成，再携手共进。
- 调用CountDownLatch的countDown方法后，当前线程并不会阻塞，会继续往下执行；
- 调用CyclicBarrier的await方法，会阻塞当前线程，直到CyclicBarrier指定的线程全部都到达了指定点的时候，才能继续往下执行；
- CountDownLatch方法比较少，操作比较简单，
- CyclicBarrier提供的方法更多，比如能够通过getNumberWaiting()，isBroken()这些方法获取当前多个线程的状态，并且CyclicBarrier的构造方法可以传入barrierAction，指定当所有线程都到达时执行的业务功能；
- CountDownLatch是不能复用的
- CyclicLatch是可以复用的。

### Semaphore与Exchanger

