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



