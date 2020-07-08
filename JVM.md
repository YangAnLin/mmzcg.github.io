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



