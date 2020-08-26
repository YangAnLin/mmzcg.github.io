# Spring IOC

Spring提供了两种容器：BeanFactory和ApplicationContext

- **BeanFactory：**基本的IoC容器，默认采用延迟初始化策略（lazy-load），即只有当客户端对象需要访问容器中某个bean对象的时候，才会对该bean对象进行初始化以及依赖注入操作。
  所以BeanFactory容器的特点是启动初期速度快，所需资源有限，适合于资源有限功能要求不严格的场景。
- **ApplicationContext：** ApplicationContext在BeanFactory基础上构建，支持其他的高级特性，如国际化，事件发布等。
  相对于BeanFactory容器来说，ApplicationContext在启动的时候即完成资源的初始化，所以启动时间较长，适合于系统资源充足，需要更多功能的场景

## Spring Bean

Java 中Bean的定义：

- 类中所有的属性都必须封装，即：使用private声明；`这个不太确定`
- 封装的属性如果需要被外部所操作，则必须编写对应的setter、getter方法；
- 一个JavaBean中至少存在一个无参构造方法。

```java
public class Staff{

    private String name;
    private int age;

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
    
}
```

而Spring IoC容器就是管理bean的工厂。
Spring中bean 是一个被实例化，组装，并通过 Spring IoC 容器所管理的对象。这些 bean 是由用容器提供的配置元数据创建的。Spring可以采用XML配置文件的方式来管理和配置Bean信息，如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
<bean id="user" class="com.wgs.spring.bean.User"></bean>
    
</beans>
```

`<beans>`是XML配置文件中根节点，下面可包含多个`<bean>`子节点。
Spring的XML配置文件中的配置与`<bean>`元素是一一对应的。

| 属性                         | 描述                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| id                           | 注册到容器的对象都有一个唯一的id值，如`id="user"`            |
| name                         | bean的别名,name可以使用逗号、空格或冒号等分割指定多个name，而id就不可以 |
| scope                        | 作用域                                                       |
| **constructor-arg**          | 用来注入依赖关系                                             |
| **properties**               | 用来注入依赖关系                                             |
| **autowiring mode**          | 用来注入依赖关系                                             |
| **lazy-initialization mode** | 是否延迟加载                                                 |
| **initialization method**    | bean被创建的时候,初始化的的方法                              |
| destruction method           | 销毁指定的方法                                               |

# Spring AOP

## 一、AOP是什么？

  AOP的全称是Aspect Orient Programming，即面向切面编程。是对OOP（Object Orient Programming）的一种补充，战门用于处理一些具有横切性质的服务。常常用于日志输出、安全控制等。

​    上面说到是对OOP的一种补充，具体补充的是什么呢？考虑一种情况，如果我们需要在所有方法执行前打印一句日志，按照OOP的处理思想，我们需要在每个业务方法开始时加入一些语句，但是我们辛辛苦苦加完之后，如果又要求在这句日志打印后再打印一句，那是不是又要加一遍？这时候你一定会想到，在某个类中编写一个日志打印方法，该方法执行这些日志打印操作，然后在每个业务方法之前加入这句方法调用，这就是面向对象编程思想。但是如果要求我们在业务方法结束时再打印一些日志呢，是不是还要去每个业务方法结束时加一遍？这样始终不是办法，而且我们总是在改业务方法，在业务方法里面掺杂了太多的其他操作，侵入性太高。

​    这时候AOP就起到作用了，我们可以编写一个切面类（Aspect），在其中的方法中来编写横切逻辑（如打印日志），然后通过配置或者注解的方式来声明该横切逻辑起作用的位置。



## 二、实现技术？

​    AOP（这里的AOP指的是面向切面编程思想，而不是Spring AOP）主要的的实现技术主要有Spring AOP和AspectJ。

1、AspectJ的底层技术。

​    AspectJ的底层技术是静态代理，即用一种AspectJ支持的特定语言编写切面，通过一个命令来编译，生成一个新的代理类，该代理类增强了业务类，这是在编译时增强，相对于下面说的运行时增强，编译时增强的性能更好。

2、Spring AOP

​    Spring AOP采用的是动态代理，在运行期间对业务方法进行增强，所以不会生成新类，对于动态代理技术，Spring AOP提供了对JDK动态代理的支持以及CGLib的支持。

​    JDK动态代理只能为接口创建动态代理实例，而不能对类创建动态代理。需要获得被目标类的接口信息（应用Java的反射技术），生成一个实现了代理接口的动态代理类（字节码），再通过反射机制获得动态代理类的构造函数，利用构造函数生成动态代理类的实例对象，在调用具体方法前调用invokeHandler方法来处理。

​    CGLib动态代理需要依赖asm包，把被代理对象类的class文件加载进来，修改其字节码生成子类。

​    但是Spring AOP基于注解配置的情况下，需要依赖于AspectJ包的标准注解，但是不需要额外的编译以及AspectJ的织入器，而基于XML配置不需要。