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

