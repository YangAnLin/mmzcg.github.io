# **1.Continue 和 Break**



# **2.List转成string,去掉两边的中括号**

```java
StringUtils.strip(partnerApplyDetail1.getList().toString().toString(),"[]");
```

# 3.**Long类型的比较**

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











