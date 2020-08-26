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

# SpringMVC

org.springframework.web.servlet.HttpServletBean#init

```java
	@Override
	public final void init() throws ServletException {
		if (logger.isDebugEnabled()) {
			logger.debug("Initializing servlet '" + getServletName() + "'");
		}

		// Set bean properties from init parameters.
		try {
            // 找导web.xml的配置文件,转换成ServletConfigPropertyValues对象
			PropertyValues pvs = new ServletConfigPropertyValues(getServletConfig(), this.requiredProperties);
            // 使用BeanWrapper构建DispatchServlet
			BeanWrapper bw = PropertyAccessorFactory.forBeanPropertyAccess(this);
			ResourceLoader resourceLoader = new ServletContextResourceLoader(getServletContext());
			bw.registerCustomEditor(Resource.class, new ResourceEditor(resourceLoader, getEnvironment()));
			initBeanWrapper(bw);
			bw.setPropertyValues(pvs, true);
		}
		catch (BeansException ex) {
			logger.error("Failed to set bean properties on servlet '" + getServletName() + "'", ex);
			throw ex;
		}

		// Let subclasses do whatever initialization they like.
        // 子类实现
		initServletBean();

		if (logger.isDebugEnabled()) {
			logger.debug("Servlet '" + getServletName() + "' configured successfully");
		}
	}
```

org.springframework.web.servlet.FrameworkServlet#initServletBean

```java
	@Override
	protected final void initServletBean() throws ServletException {
		getServletContext().log("Initializing Spring FrameworkServlet '" + getServletName() + "'");
		if (this.logger.isInfoEnabled()) {
			this.logger.info("FrameworkServlet '" + getServletName() + "': initialization started");
		}
		long startTime = System.currentTimeMillis();

		try {
            // 初始化spring上下文
			this.webApplicationContext = initWebApplicationContext();
            // 子类实现
			initFrameworkServlet();
		}
		catch (ServletException ex) {
			this.logger.error("Context initialization failed", ex);
			throw ex;
		}
		catch (RuntimeException ex) {
			this.logger.error("Context initialization failed", ex);
			throw ex;
		}

		if (this.logger.isInfoEnabled()) {
			long elapsedTime = System.currentTimeMillis() - startTime;
			this.logger.info("FrameworkServlet '" + getServletName() + "': initialization completed in " +
					elapsedTime + " ms");
		}
	}
```

org.springframework.web.servlet.FrameworkServlet#initWebApplicationContext

```java
protected WebApplicationContext initWebApplicationContext() {
    	// 得到跟上下文
        WebApplicationContext rootContext =
        WebApplicationContextUtils.getWebApplicationContext(getServletContext());
    WebApplicationContext wac = null;

    if (this.webApplicationContext != null) {
        // A context instance was injected at construction time -> use it
        wac = this.webApplicationContext;
        if (wac instanceof ConfigurableWebApplicationContext) {
            ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext) wac;
            if (!cwac.isActive()) {
                // The context has not yet been refreshed -> provide services such as
                // setting the parent context, setting the application context id, etc
                if (cwac.getParent() == null) {
                    // The context instance was injected without an explicit parent -> set
                    // the root application context (if any; may be null) as the parent
                    cwac.setParent(rootContext);
                }
                configureAndRefreshWebApplicationContext(cwac);
            }
        }
    }
    if (wac == null) {
        // No context instance was injected at construction time -> see if one
        // has been registered in the servlet context. If one exists, it is assumed
        // that the parent context (if any) has already been set and that the
        // user has performed any initialization such as setting the context id
        wac = findWebApplicationContext();
    }
    if (wac == null) {
        // No context instance is defined for this servlet -> create a local one
        wac = createWebApplicationContext(rootContext);
    }

    if (!this.refreshEventReceived) {
        // Either the context is not a ConfigurableApplicationContext with refresh
        // support or the context injected at construction time had already been
        // refreshed -> trigger initial onRefresh manually here.
        onRefresh(wac);
    }

    if (this.publishContext) {
        // Publish the context as a servlet context attribute.
        String attrName = getServletContextAttributeName();
        getServletContext().setAttribute(attrName, wac);
        if (this.logger.isDebugEnabled()) {
            this.logger.debug("Published WebApplicationContext of servlet '" + getServletName() +
                              "' as ServletContext attribute with name [" + attrName + "]");
        }
    }

    return wac;
}

```

org.springframework.web.servlet.DispatcherServlet#onRefresh

```java
@Override
protected void onRefresh(ApplicationContext context) {
    initStrategies(context);
}

/**
	 * Initialize the strategy objects that this servlet uses.
	 * <p>May be overridden in subclasses in order to initialize further strategy objects.
	 */
// 初始化各种策略
protected void initStrategies(ApplicationContext context) {
    initMultipartResolver(context);
    initLocaleResolver(context);
    initThemeResolver(context);
    // 找到正确的controller
    initHandlerMappings(context);
    initHandlerAdapters(context);
    // 异常机制
    initHandlerExceptionResolvers(context);
    initRequestToViewNameTranslator(context);
    // 视图机制
    initViewResolvers(context);
    initFlashMapManager(context);
}
```

# SpringMCC临时用的

https://zhuanlan.zhihu.com/p/62562499

## 设置属性

```java
// 1. 设置属性
// Make web application context available
request.setAttribute(WEB_APPLICATION_CONTEXT_ATTRIBUTE, getWebApplicationContext());

// Make locale resolver available
request.setAttribute(LOCALE_RESOLVER_ATTRIBUTE, this.localeResolver);

// Make theme resolver available
request.setAttribute(THEME_RESOLVER_ATTRIBUTE, this.themeResolver);
```

## 根据 Request 请求的 URL 得到对应的 handler 执行链，其实就是拦截器和 Controller 代理对象。

```java
// 2. 找 handler 返回执行链
HandlerExecutionChain mappedHandler = getHandler(request);
```

## 得到 handler 的适配器

```java
// This will throw an exception if no adapter is found
// 3. 返回 handler 的适配器
HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
```

## 循环执行 handler 的 pre 拦截器

```java
// 4. 循环执行 handler 的 pre 拦截器
for (int i = 0; i < mappedHandler.getInterceptors().length; i++) {
    HandlerInterceptor interceptor = mappedHandler.getInterceptors()[i];
    // pre 拦截器
    if (!interceptor.preHandle(request, response, mappedHandler.getHandler())) {
        return;
    }
}
```

## 执行真正的 handler，并返回 ModelAndView(Handler 是个代理对象，可能会执行 AOP )

```java
// 5. 执行真正的 handler，并返回  ModelAndView(Handler 是个代理对象，可能会执行 AOP )
ModelAndView mv = ha.handle(request, response, mappedHandler.getHandler());
```

## 循环执行 handler 的 post 拦截器

```java
// 6. 循环执行 handler 的 post 拦截器
for (int i = mappedHandler.getInterceptors().length - 1; i >=0 ; i--) {
    HandlerInterceptor interceptor = mappedHandler.getInterceptors()[i];
    // post 拦截器
    interceptor.postHandle(request, response, mappedHandler.getHandler());
}

# 根据 ModelAndView 信息得到 View 实例
  View view = null;
if (mv.isReference()) {
    // We need to resolve this view name
    // 7. 根据 ModelAndView 信息得到 View 实例
    view = this.viewResolver.resolveViewName(mv.getViewName(), locale);
}


# 渲染 View 返回
// 8. 渲染 View 返回
view.render(mv.getModel(), request, response);
```

其实理解这些才是最重要的。

1. 用户发送请求至前端控制器DispatcherServlet
2. DispatcherServlet收到请求调用HandlerMapping处理器映射器。
3. 处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。
4. DispatcherServlet通过HandlerAdapter处理器适配器调用处理器
5. HandlerAdapter执行处理器(handler，也叫后端控制器)。
6. Controller执行完成返回ModelAndView
7. HandlerAdapter将handler执行结果ModelAndView返回给DispatcherServlet
8. DispatcherServlet将ModelAndView传给ViewReslover视图解析器
9. ViewReslover解析后返回具体View对象
10. DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）。
11. DispatcherServlet响应用户