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

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226164738.png)

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226164726.png)



## Spring Bean 生命周期

2.低昂registerBeanFactoryPostProcessor  完成扫描,运行之前,不会有我们自己的类,除了`@componentScan`这个注解的这个类,等完成之后,就会有我们自己的类

1：实例化一个ApplicationContext的对象；
2：调用bean工厂后置处理器完成扫描；
3：循环解析扫描出来的类信息；
4：实例化一个BeanDefinition对象来存储解析出来的信息；
5：把实例化好的beanDefinition对象put到`beanDefinitionMap`当中缓存起来，以便后面实例化bean；
6：再次调用bean工厂后置处理器；
7：当然spring还会干很多事情，比如国际化，比如注册BeanPostProcessor等等，如果我们只关心如何实例化一个bean的话那么这一步就是spring调用`finishBeanFactoryInitialization`方法来实例化单例的bean，实例化之前spring要做验证，需要遍历所有扫描出来的类，依次判断这个bean是否Lazy，是否prototype，是否abstract等等；
8：如果验证完成spring在实例化一个bean之前需要推断构造方法，因为spring实例化对象是通过构造方法反射，故而需要知道用哪个构造方法；
9：推断完构造方法之后spring调用构造方法反射实例化一个**对象**；注意我这里说的是对象、对象、对象；这个时候对象已经实例化出来了，但是并不是一个完整的bean，最简单的体现是这个时候实例化出来的对象属性是没有注入，所以不是一个完整的bean；
10：spring处理合并后的beanDefinition(合并？是spring当中非常重要的一块内容，后面的文章我会分析)；
11：判断是否支持循环依赖，如果支持则提前把一个工厂存入singletonFactories——map；
12：判断是否需要完成属性注入
13：如果需要完成属性注入，则开始注入属性
14：判断bean的类型回调Aware接口
15：调用生命周期回调方法
16：如果需要代理则完成代理
17：put到单例池——bean完成——存在spring容器当中

## Spring Bean 循环依赖

> https://juejin.im/post/6844904166351978504#h5

AnnotationConfigApplicationContext#AnnotationConfigApplicationContext

```java
public AnnotationConfigApplicationContext(Class<?>... componentClasses) {
    this();
    register(componentClasses);
    // 关键方法
    refresh();
}
```

org.springframework.context.support.AbstractApplicationContext#refresh

```java
@Override
public void refresh() throws BeansException, IllegalStateException {
    synchronized (this.startupShutdownMonitor) {
        // Prepare this context for refreshing.
        prepareRefresh();

        // Tell the subclass to refresh the internal bean factory.
        ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

        // Prepare the bean factory for use in this context.
        prepareBeanFactory(beanFactory);

        try {
            // Allows post-processing of the bean factory in context subclasses.
            postProcessBeanFactory(beanFactory);

            // Invoke factory processors registered as beans in the context.
            invokeBeanFactoryPostProcessors(beanFactory);

            // Register bean processors that intercept bean creation.
            registerBeanPostProcessors(beanFactory);

            // Initialize message source for this context.
            initMessageSource();

            // Initialize event multicaster for this context.
            // 完成所有的扫描
            initApplicationEventMulticaster();

            // Initialize other special beans in specific context subclasses.
            onRefresh();

            // Check for listener beans and register them.
            registerListeners();

            // Instantiate all remaining (non-lazy-init) singletons.
            // 实例化所有没有延迟的单例类
            finishBeanFactoryInitialization(beanFactory);

            // Last step: publish corresponding event.
            finishRefresh();
        }
    }
}
```

org.springframework.context.support.AbstractApplicationContext#finishBeanFactoryInitialization

```java
protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {
    // ....

    // Instantiate all remaining (non-lazy-init) singletons.
    // 实例化所有单例,非lazy
    beanFactory.preInstantiateSingletons();
}
```

org.springframework.beans.factory.support.DefaultListableBeanFactory#preInstantiateSingletons

```java
@Override
public void preInstantiateSingletons() throws BeansException {
    if (logger.isTraceEnabled()) {
        logger.trace("Pre-instantiating singletons in " + this);
    }

    // Iterate over a copy to allow for init methods which in turn register new bean definitions.
    // While this may not be part of the regular factory bootstrap, it does otherwise work fine.
    List<String> beanNames = new ArrayList<>(this.beanDefinitionNames);

    // Trigger initialization of all non-lazy singleton beans...
    for (String beanName : beanNames) {
        RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);
        // 验证,判断这个是是不是抽象的和是不是单例的和是不是延迟加载的
        if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) {
            if (isFactoryBean(beanName)) {
                Object bean = getBean(FACTORY_BEAN_PREFIX + beanName);
                if (bean instanceof FactoryBean) {
                    FactoryBean<?> factory = (FactoryBean<?>) bean;
                    boolean isEagerInit;
                    if (System.getSecurityManager() != null && factory instanceof SmartFactoryBean) {
                        isEagerInit = AccessController.doPrivileged(
                            (PrivilegedAction<Boolean>) ((SmartFactoryBean<?>) factory)::isEagerInit,
                            getAccessControlContext());
                    }
                    else {
                        isEagerInit = (factory instanceof SmartFactoryBean &&
                                       ((SmartFactoryBean<?>) factory).isEagerInit());
                    }
                    if (isEagerInit) {
                        getBean(beanName);
                    }
                }
            }
            else {
                // 验证一切都通过的类,开始实例化普通的bean,还不是spring bean
                getBean(beanName);
            }
        }
    }

    // ....
}
```

org.springframework.beans.factory.support.AbstractBeanFactory#getBean(java.lang.String)

```java
@Override
public Object getBean(String name) throws BeansException {
    return doGetBean(name, null, null, false);
}
```

org.springframework.beans.factory.support.AbstractBeanFactory#doGetBean

这里面大部分都是验证,比如depenon,或者import

```java
protected <T> T doGetBean(
			String name, @Nullable Class<T> requiredType, @Nullable Object[] args, boolean typeCheckOnly)
    throws BeansException {

    // 理解bean的名字是否非法
    String beanName = transformedBeanName(name);
    Object bean;

    // Eagerly check singleton cache for manually registered singletons.
    // 这里的方法啊
    Object sharedInstance = getSingleton(beanName);
    if (sharedInstance != null && args == null) {
        bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);
    }

    else {
        // Fail if we're already creating this bean instance:
        // We're assumably within a circular reference.
        // 判断这个类是不是在创建过程中,循环依赖的时候要用
        if (isPrototypeCurrentlyInCreation(beanName)) {
            throw new BeanCurrentlyInCreationException(beanName);
        }

        // Check if bean definition exists in this factory.
        BeanFactory parentBeanFactory = getParentBeanFactory();
        if (parentBeanFactory != null && !containsBeanDefinition(beanName)) {
            // 方法注入
            // Not found -> check parent.
            String nameToLookup = originalBeanName(name);
            if (parentBeanFactory instanceof AbstractBeanFactory) {
                return ((AbstractBeanFactory) parentBeanFactory).doGetBean(
                    nameToLookup, requiredType, args, typeCheckOnly);
            }
            else if (args != null) {
                // Delegation to parent with explicit args.
                return (T) parentBeanFactory.getBean(nameToLookup, args);
            }
            else if (requiredType != null) {
                // No args -> delegate to standard getBean method.
                return parentBeanFactory.getBean(nameToLookup, requiredType);
            }
            else {
                return (T) parentBeanFactory.getBean(nameToLookup);
            }
        }

        if (!typeCheckOnly) {
            markBeanAsCreated(beanName);
        }

        try {
            RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);
            checkMergedBeanDefinition(mbd, beanName, args);

            // Guarantee initialization of beans that the current bean depends on.
            String[] dependsOn = mbd.getDependsOn();
            if (dependsOn != null) {
                for (String dep : dependsOn) {
                    if (isDependent(beanName, dep)) {
                        throw new BeanCreationException(mbd.getResourceDescription(), beanName,
                                                        "Circular depends-on relationship between '" + beanName + "' and '" + dep + "'");
                    }
                    registerDependentBean(dep, beanName);
                    try {
                        getBean(dep);
                    }
                    catch (NoSuchBeanDefinitionException ex) {
                        throw new BeanCreationException(mbd.getResourceDescription(), beanName,
                                                        "'" + beanName + "' depends on missing bean '" + dep + "'", ex);
                    }
                }
            }

            // Create bean instance.
            // 判断类是不是单例
            if (mbd.isSingleton()) {
                // getSingleton(String,facotory) 这个方法里有正在创建中的标识设置
                sharedInstance = getSingleton(beanName, () -> {
                    try {
                        // 完成了目标对象的创建
                        // 如果需要代理,还创建了代理
                        return createBean(beanName, mbd, args);
                    }
                    catch (BeansException ex) {
                        // Explicitly remove instance from singleton cache: It might have been put there
                        // eagerly by the creation process, to allow for circular reference resolution.
                        // Also remove any beans that received a temporary reference to the bean.
                        destroySingleton(beanName);
                        throw ex;
                    }
                });
                bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
            }

            else if (mbd.isPrototype()) {
                // It's a prototype -> create a new instance.
                Object prototypeInstance = null;
                try {
                    beforePrototypeCreation(beanName);
                    prototypeInstance = createBean(beanName, mbd, args);
                }
                finally {
                    afterPrototypeCreation(beanName);
                }
                bean = getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);
            }

            else {
                String scopeName = mbd.getScope();
                if (!StringUtils.hasLength(scopeName)) {
                    throw new IllegalStateException("No scope name defined for bean ´" + beanName + "'");
                }
                Scope scope = this.scopes.get(scopeName);
                if (scope == null) {
                    throw new IllegalStateException("No Scope registered for scope name '" + scopeName + "'");
                }
                try {
                    Object scopedInstance = scope.get(beanName, () -> {
                        beforePrototypeCreation(beanName);
                        try {
                            return createBean(beanName, mbd, args);
                        }
                        finally {
                            afterPrototypeCreation(beanName);
                        }
                    });
                    bean = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);
                }
                catch (IllegalStateException ex) {
                    throw new BeanCreationException(beanName,
                                                    "Scope '" + scopeName + "' is not active for the current thread; consider " +
                                                    "defining a scoped proxy for this bean if you intend to refer to it from a singleton",
                                                    ex);
                }
            }
        }
        catch (BeansException ex) {
            cleanupAfterBeanCreationFailure(beanName);
            throw ex;
        }
    }

    // Check if required type matches the type of the actual bean instance.
    if (requiredType != null && !requiredType.isInstance(bean)) {
        try {
            T convertedBean = getTypeConverter().convertIfNecessary(bean, requiredType);
            if (convertedBean == null) {
                throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
            }
            return convertedBean;
        }
        catch (TypeMismatchException ex) {
            if (logger.isTraceEnabled()) {
                logger.trace("Failed to convert bean '" + name + "' to required type '" +
                             ClassUtils.getQualifiedName(requiredType) + "'", ex);
            }
            throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
        }
    }
    return (T) bean;
}
```

org.springframework.beans.factory.support.DefaultSingletonBeanRegistry#getSingleton(java.lang.String)

```java
// 上个代码块第七行调用的
@Override
@Nullable
public Object getSingleton(String beanName) {
    return getSingleton(beanName, true);
}


/** Cache of singleton objects: bean name to bean instance. */
/** 缓存单例对象: bean name to bean instance. */
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

@Nullable
protected Object getSingleton(String beanName, boolean allowEarlyReference) {
    // 初始化的时候这里肯定是null,但是在初始化完成之后,再调用getBean就肯定不是null
    // isSingletonCurrentlyInCreation 这个方法很重要,说明对象是不是正在创建
    // singletonFactories 也很重要
    Object singletonObject = this.singletonObjects.get(beanName);
    
    // 判断循环依赖的时候
    if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
        synchronized (this.singletonObjects) {
            singletonObject = this.earlySingletonObjects.get(beanName);
            if (singletonObject == null && allowEarlyReference) {
                ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
                if (singletonFactory != null) {
                    singletonObject = singletonFactory.getObject();
                    this.earlySingletonObjects.put(beanName, singletonObject);
                    this.singletonFactories.remove(beanName);
                }
            }
        }
    }
    return singletonObject;
}
```

org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#createBean

```java
@Override
	protected Object createBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
			throws BeanCreationException {

		if (logger.isTraceEnabled()) {
			logger.trace("Creating instance of bean '" + beanName + "'");
		}
		RootBeanDefinition mbdToUse = mbd;

		// Make sure bean class is actually resolved at this point, and
		// clone the bean definition in case of a dynamically resolved Class
		// which cannot be stored in the shared merged bean definition.
        // 从beanDefinition对象中获取出来bean的类型
		Class<?> resolvedClass = resolveBeanClass(mbd, beanName);
		if (resolvedClass != null && !mbd.hasBeanClass() && mbd.getBeanClassName() != null) {
			mbdToUse = new RootBeanDefinition(mbd);
			mbdToUse.setBeanClass(resolvedClass);
		}

		// Prepare method overrides.
		try {
			mbdToUse.prepareMethodOverrides();
		}
		catch (BeanDefinitionValidationException ex) {
			throw new BeanDefinitionStoreException(mbdToUse.getResourceDescription(),
					beanName, "Validation of method overrides failed", ex);
		}

		try {
			// Give BeanPostProcessors a chance to return a proxy instead of the target bean instance.
            // 第一次调用个后置处理器
			Object bean = resolveBeforeInstantiation(beanName, mbdToUse);
			if (bean != null) {
				return bean;
			}
		}
		catch (Throwable ex) {
			throw new BeanCreationException(mbdToUse.getResourceDescription(), beanName,
					"BeanPostProcessor before instantiation of bean failed", ex);
		}

		try {
            // 调用方法
			Object beanInstance = doCreateBean(beanName, mbdToUse, args);
			if (logger.isTraceEnabled()) {
				logger.trace("Finished creating instance of bean '" + beanName + "'");
			}
			return beanInstance;
		}
		catch (BeanCreationException | ImplicitlyAppearedSingletonException ex) {
			// A previously detected exception with proper bean creation context already,
			// or illegal singleton state to be communicated up to DefaultSingletonBeanRegistry.
			throw ex;
		}
		catch (Throwable ex) {
			throw new BeanCreationException(
					mbdToUse.getResourceDescription(), beanName, "Unexpected exception during bean creation", ex);
		}
	}
```

org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#doCreateBean

```java
protected Object doCreateBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
			throws BeanCreationException {

    // Instantiate the bean.
    BeanWrapper instanceWrapper = null;
    if (mbd.isSingleton()) {
        instanceWrapper = this.factoryBeanInstanceCache.remove(beanName);
    }
    if (instanceWrapper == null) {
        // 实例化对象,里面第二次调调用后置处理器
        // 反射调用对象的构造方法
        // 这里java对象就已经有了
        instanceWrapper = createBeanInstance(beanName, mbd, args);
    }
    Object bean = instanceWrapper.getWrappedInstance();
    Class<?> beanType = instanceWrapper.getWrappedClass();
    if (beanType != NullBean.class) {
        mbd.resolvedTargetType = beanType;
    }

    // Allow post-processors to modify the merged bean definition.
    synchronized (mbd.postProcessingLock) {
        if (!mbd.postProcessed) {
            try {
                // 第三次调用后置处理器
                applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);
            }
  
            mbd.postProcessed = true;
        }
    }

    // Eagerly cache singletons to be able to resolve circular references
    // even when triggered by lifecycle interfaces like BeanFactoryAware.
    // 判断是否需要循环依赖
    boolean earlySingletonExposure = 
        // 到这里了,也肯定是true
        (mbd.isSingleton() && 
         // 默认值是true
         this.allowCircularReferences &&
		isSingletonCurrentlyInCreation(beanName));
    
    if (earlySingletonExposure) {
        // 第四次调用后置处理器,判断是否需要AOP
        addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));
    }

    // Initialize the bean instance.
    Object exposedObject = bean;
    try {
        // 填充属性,也就是我们说的自动注入
        // 里面会完成第五次和第六次后置处理器的调用
        // 看这里
        populateBean(beanName, mbd, instanceWrapper);
        // 初始化spring
        // 里面会进行第七次和第八次后置处理的调用个
        exposedObject = initializeBean(beanName, exposedObject, mbd);
    }
    catch (Throwable ex) {
        if (ex instanceof BeanCreationException && beanName.equals(((BeanCreationException) ex).getBeanName())) {
            throw (BeanCreationException) ex;
        }
        else {
            throw new BeanCreationException(
                mbd.getResourceDescription(), beanName, "Initialization of bean failed", ex);
        }
    }

    if (earlySingletonExposure) {
        Object earlySingletonReference = getSingleton(beanName, false);
        if (earlySingletonReference != null) {
            if (exposedObject == bean) {
                exposedObject = earlySingletonReference;
            }
            else if (!this.allowRawInjectionDespiteWrapping && hasDependentBean(beanName)) {
                String[] dependentBeans = getDependentBeans(beanName);
                Set<String> actualDependentBeans = new LinkedHashSet<>(dependentBeans.length);
                for (String dependentBean : dependentBeans) {
                    if (!removeSingletonIfCreatedForTypeCheckOnly(dependentBean)) {
                        actualDependentBeans.add(dependentBean);
                    }
                }
                // 省略代码
            }
        }
    }

    // Register bean as disposable.
    try {
        registerDisposableBeanIfNecessary(beanName, bean, mbd);
    }
    catch (BeanDefinitionValidationException ex) {
        throw new BeanCreationException(
            mbd.getResourceDescription(), beanName, "Invalid destruction signature", ex);
    }

    return exposedObject;
}
```

org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#createBeanInstance

```java
protected BeanWrapper createBeanInstance(String beanName, RootBeanDefinition mbd, @Nullable Object[] args) {
    // Make sure bean class is actually resolved at this point.
    Class<?> beanClass = resolveBeanClass(mbd, beanName);

    if (beanClass != null && !Modifier.isPublic(beanClass.getModifiers()) && !mbd.isNonPublicAccessAllowed()) {
        throw new BeanCreationException(mbd.getResourceDescription(), beanName,
                                        "Bean class isn't public, and non-public access not allowed: " + beanClass.getName());
    }

    Supplier<?> instanceSupplier = mbd.getInstanceSupplier();
    if (instanceSupplier != null) {
        return obtainFromSupplier(instanceSupplier, beanName);
    }

    if (mbd.getFactoryMethodName() != null) {
        return instantiateUsingFactoryMethod(beanName, mbd, args);
    }

    // Shortcut when re-creating the same bean...
    boolean resolved = false;
    boolean autowireNecessary = false;
    if (args == null) {
        synchronized (mbd.constructorArgumentLock) {
            if (mbd.resolvedConstructorOrFactoryMethod != null) {
                resolved = true;
                autowireNecessary = mbd.constructorArgumentsResolved;
            }
        }
    }
    if (resolved) {
        if (autowireNecessary) {
            return autowireConstructor(beanName, mbd, null, null);
        }
        else {
            return instantiateBean(beanName, mbd);
        }
    }

    // Candidate constructors for autowiring?
    // 第二次调用后置处理器构造方法,通过反射实例化对象,这时候构造方法里有打印,就会打印出日志
    Constructor<?>[] ctors = determineConstructorsFromBeanPostProcessors(beanClass, beanName);
    if (ctors != null || mbd.getResolvedAutowireMode() == AUTOWIRE_CONSTRUCTOR ||
        mbd.hasConstructorArgumentValues() || !ObjectUtils.isEmpty(args)) {
        return autowireConstructor(beanName, mbd, ctors, args);
    }

    // Preferred constructors for default construction?
    ctors = mbd.getPreferredConstructors();
    if (ctors != null) {
        return autowireConstructor(beanName, mbd, ctors, null);
    }

    // No special handling: simply use no-arg constructor.
    return instantiateBean(beanName, mbd);
}
```

org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#populateBean

```java
protected void populateBean(String beanName, RootBeanDefinition mbd, @Nullable BeanWrapper bw) {
    if (bw == null) {
        if (mbd.hasPropertyValues()) {
            throw new BeanCreationException(
                mbd.getResourceDescription(), beanName, "Cannot apply property values to null instance");
        }
        else {
            // Skip property population phase for null instance.
            return;
        }
    }

    // Give any InstantiationAwareBeanPostProcessors the opportunity to modify the
    // state of the bean before properties are set. This can be used, for example,
    // to support styles of field injection.
    if (!mbd.isSynthetic() && hasInstantiationAwareBeanPostProcessors()) {
        for (BeanPostProcessor bp : getBeanPostProcessors()) {
            if (bp instanceof InstantiationAwareBeanPostProcessor) {
                InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
                if (!ibp.postProcessAfterInstantiation(bw.getWrappedInstance(), beanName)) {
                    return;
                }
            }
        }
    }

    PropertyValues pvs = (mbd.hasPropertyValues() ? mbd.getPropertyValues() : null);

    int resolvedAutowireMode = mbd.getResolvedAutowireMode();
    if (resolvedAutowireMode == AUTOWIRE_BY_NAME || resolvedAutowireMode == AUTOWIRE_BY_TYPE) {
        MutablePropertyValues newPvs = new MutablePropertyValues(pvs);
        // Add property values based on autowire by name if applicable.
        if (resolvedAutowireMode == AUTOWIRE_BY_NAME) {
            autowireByName(beanName, mbd, bw, newPvs);
        }
        // Add property values based on autowire by type if applicable.
        if (resolvedAutowireMode == AUTOWIRE_BY_TYPE) {
            autowireByType(beanName, mbd, bw, newPvs);
        }
        pvs = newPvs;
    }

    boolean hasInstAwareBpps = hasInstantiationAwareBeanPostProcessors();
    boolean needsDepCheck = (mbd.getDependencyCheck() != AbstractBeanDefinition.DEPENDENCY_CHECK_NONE);

    PropertyDescriptor[] filteredPds = null;
    if (hasInstAwareBpps) {
        if (pvs == null) {
            pvs = mbd.getPropertyValues();
        }
        for (BeanPostProcessor bp : getBeanPostProcessors()) {
            if (bp instanceof InstantiationAwareBeanPostProcessor) {
                // 这里的ibp常用的有两种类型
                // 1.@Resouce 使用的是CommonAnnotationBeanPostProcessor
				// 2.@Autowire 使用的是AutoWireAnnotationBeanPostProcessor
                InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
                // 这里会调用属性的注入,也就是在这里,碰到循环依赖的时候,就会调用个
                // org.springframework.beans.factory.support.DefaultSingletonBeanRegistry#getSingleton
                PropertyValues pvsToUse = ibp.postProcessProperties(pvs, bw.getWrappedInstance(), beanName);
                if (pvsToUse == null) {
                    if (filteredPds == null) {
                        filteredPds = filterPropertyDescriptorsForDependencyCheck(bw, mbd.allowCaching);
                    }
                    pvsToUse = ibp.postProcessPropertyValues(pvs, filteredPds, bw.getWrappedInstance(), beanName);
                    if (pvsToUse == null) {
                        return;
                    }
                }
                pvs = pvsToUse;
            }
        }
    }
    if (needsDepCheck) {
        if (filteredPds == null) {
            filteredPds = filterPropertyDescriptorsForDependencyCheck(bw, mbd.allowCaching);
        }
        checkDependencies(beanName, mbd, filteredPds, pvs);
    }

    if (pvs != null) {
        applyPropertyValues(beanName, mbd, bw, pvs);
    }
}
```

org.springframework.beans.factory.support.DefaultSingletonBeanRegistry#getSingleton(java.lang.String, org.springframework.beans.factory.ObjectFactory<?>)

```java
public Object getSingleton(String beanName, ObjectFactory<?> singletonFactory) {
    Assert.notNull(beanName, "Bean name must not be null");
    synchronized (this.singletonObjects) {
        Object singletonObject = this.singletonObjects.get(beanName);
        if (singletonObject == null) {
            if (this.singletonsCurrentlyInDestruction) {
                throw new BeanCreationNotAllowedException(beanName,
                                                          "Singleton bean creation not allowed while singletons of this factory are in destruction " +
                                                          "(Do not request a bean from a BeanFactory in a destroy method implementation!)");
            }
            if (logger.isDebugEnabled()) {
                logger.debug("Creating shared instance of singleton bean '" + beanName + "'");
            }
            // 重点,如果没有获取到,就设置个标识,表示正在创建
            beforeSingletonCreation(beanName);
            boolean newSingleton = false;
            boolean recordSuppressedExceptions = (this.suppressedExceptions == null);
            if (recordSuppressedExceptions) {
                this.suppressedExceptions = new LinkedHashSet<>();
            }
            try {
                singletonObject = singletonFactory.getObject();
                newSingleton = true;
            }
            catch (IllegalStateException ex) {
                // Has the singleton object implicitly appeared in the meantime ->
                // if yes, proceed with it since the exception indicates that state.
                singletonObject = this.singletonObjects.get(beanName);
                if (singletonObject == null) {
                    throw ex;
                }
            }
            catch (BeanCreationException ex) {
                if (recordSuppressedExceptions) {
                    for (Exception suppressedException : this.suppressedExceptions) {
                        ex.addRelatedCause(suppressedException);
                    }
                }
                throw ex;
            }
            finally {
                if (recordSuppressedExceptions) {
                    this.suppressedExceptions = null;
                }
                afterSingletonCreation(beanName);
            }
            if (newSingleton) {
                addSingleton(beanName, singletonObject);
            }
        }
        return singletonObject;
    }
}
```

org.springframework.beans.factory.support.DefaultSingletonBeanRegistry#beforeSingletonCreation

```java
/** Names of beans that are currently in creation. */
// 添加到这里来了之后就标识当前这个bean正在创建
private final Set<String> singletonsCurrentlyInCreation =
    Collections.newSetFromMap(new ConcurrentHashMap<>(16));

protected void beforeSingletonCreation(String beanName) {
    if (!this.inCreationCheckExclusions.contains(beanName) && !this.singletonsCurrentlyInCreation.add(beanName)) {
        throw new BeanCurrentlyInCreationException(beanName);
    }
}
```

org.springframework.beans.factory.support.DefaultSingletonBeanRegistry#addSingletonFactory

二级缓存

```java
protected void addSingletonFactory(String beanName, ObjectFactory<?> singletonFactory) {
    Assert.notNull(singletonFactory, "Singleton factory must not be null");
    synchronized (this.singletonObjects) {
        if (!this.singletonObjects.containsKey(beanName)) {
            this.singletonFactories.put(beanName, singletonFactory);
            this.earlySingletonObjects.remove(beanName);
            this.registeredSingletons.add(beanName);
        }
    }
}
```



### 三个缓存

```java
// singletonObjects：第一级缓存，里面存放的都是创建好的成品Bean。
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object(256);

// earlySingletonObjects : 第二级缓存，里面存放的都是半成品的Bean
private final Map<String, Object> earlySingletonObjects = new HashMap<String, Object(16);

// singletonFactories ：第三级缓存， 不同于前两个存的是 Bean对象引用，此缓存存的bean 工厂对象，也就存的是 专门创建Bean的一个工厂对象。此缓存用于解决循环依赖
private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<String, ObjectFactory<?>>(16);
```



### 两个缓存能解决不

A引用创建后，提前暴露到`半成品缓存中`

依赖B，创建B ，B填充属性时发现依赖A， `先从成品缓存查找，没有,再从半成品缓存查找` 取到A的`早期引用`。

```
B顺利走完创建过程`, 将`B的早期引用从半成品缓存移动到成品缓存
```

B创建完成，A获取到B的引用，继续创建。

A创建完成，将`A的早期引用从半成品缓存移动到成品缓存`

### 为啥需要三个缓存

上面两个缓存的地方，我们只是没有考虑代理的情况。

Bean在创建的最后阶段，会检查是否需要创建代理，如果创建了代理，那么最终返回的就是代理实例的引用。我们通过beanname获取到最终是代理实例的引用

也就是说：假设A最终会创建代理，提前暴露A的引用， B填充属性时填充的是A的原始对象引用。A最终放入成品库里是代理的引用。那么B中依然是A的早期引用。这种结果最终会与我们的期望的大相径庭了。

### 完整的流程

关键点：



- A绑定到ObjectFactory 注册到`工厂缓存singletonFactory`中，
- B在填充A时，`先查成品缓存`有没有，`再查半成品缓存`有没有，`最后看工厂缓存有没有单例工厂类`，有A的ObjectFactory。调用getObject ，执行扩展逻辑，可能返回的代理引用，也可能返回原始引用。
- 成功获取到A的早期引用，将A放入到`半成品缓存`中，B填充A引用完毕。
- 代理问题， 循环依赖问题都解决了

## Spring Bean 二次开发

在实例化Bean之前,Spring会调用扩展的类,实现`BeanFactoryPostProcessor`,并且机上`@component`注解,如果没有实现,spring就不会调用

# Spring AOP

## AOP是什么

  AOP的全称是Aspect Orient Programming，即面向切面编程。是对OOP（Object Orient Programming）的一种补充，战门用于处理一些具有横切性质的服务。常常用于日志输出、安全控制等。

​    上面说到是对OOP的一种补充，具体补充的是什么呢？考虑一种情况，如果我们需要在所有方法执行前打印一句日志，按照OOP的处理思想，我们需要在每个业务方法开始时加入一些语句，但是我们辛辛苦苦加完之后，如果又要求在这句日志打印后再打印一句，那是不是又要加一遍？这时候你一定会想到，在某个类中编写一个日志打印方法，该方法执行这些日志打印操作，然后在每个业务方法之前加入这句方法调用，这就是面向对象编程思想。但是如果要求我们在业务方法结束时再打印一些日志呢，是不是还要去每个业务方法结束时加一遍？这样始终不是办法，而且我们总是在改业务方法，在业务方法里面掺杂了太多的其他操作，侵入性太高。

​    这时候AOP就起到作用了，我们可以编写一个切面类（Aspect），在其中的方法中来编写横切逻辑（如打印日志），然后通过配置或者注解的方式来声明该横切逻辑起作用的位置。

## 实现技术

AOP（这里的AOP指的是面向切面编程思想，而不是Spring AOP）主要的的实现技术主要有Spring AOP和AspectJ。

1、AspectJ的底层技术。

​    AspectJ的底层技术是静态代理，即用一种AspectJ支持的特定语言编写切面，通过一个命令来编译，生成一个新的代理类，该代理类增强了业务类，这是在编译时增强，相对于下面说的运行时增强，编译时增强的性能更好。

2、Spring AOP

​    Spring AOP采用的是动态代理，在运行期间对业务方法进行增强，所以不会生成新类，对于动态代理技术，Spring AOP提供了对JDK动态代理的支持以及CGLib的支持。

​    JDK动态代理只能为接口创建动态代理实例，而不能对类创建动态代理。需要获得被目标类的接口信息（应用Java的反射技术），生成一个实现了代理接口的动态代理类（字节码），再通过反射机制获得动态代理类的构造函数，利用构造函数生成动态代理类的实例对象，在调用具体方法前调用invokeHandler方法来处理。

​    CGLib动态代理需要依赖asm包，把被代理对象类的class文件加载进来，修改其字节码生成子类。

​    但是Spring AOP基于注解配置的情况下，需要依赖于AspectJ包的标准注解，但是不需要额外的编译以及AspectJ的织入器，而基于XML配置不需要。

## 知识点

### PointCut

你想要去切某个东西之前总得先知道要在哪里切入是吧，切点格式如下：`execution(* com.nuofankj.springdemo.aop.*Service.*(..))`格式使用了正常表达式来定义那个范围内的类、那些接口会被当成切点

### **Advice** 

通知,所谓的Advice其实就是定义了Aop何时被调用，确实有种通知的感觉

- Before 在方法被调用之前调用
- After 在方法完成之后调用
- After-returning 在方法成功执行之后调用
- After-throwing 在方法抛出异常之后调用
- Around 在被通知的方法调用之前和调用之后调用

### JoinPoint

JoinPoint连接点，其实很好理解，上面又有通知、又有切点，那和具体业务的连接点又是什么呢？没错，其实就是对应业务的方法对象，因为我们在横切代码中是有可能需要用到具体方法中的具体数据的，而连接点便可以做到这一点。

### Aspect

就是我们关注点的模块化。这个关注点可能会横切多个对象和模块，事务管理是横切关注点的很好的例子。它是一个抽象的概念，从软件的角度来说是指在应用程序不同模块中的某一个领域或方面。又pointcut 和advice组成。

### Weaving

把切面应用到目标对象来创建新的 advised 对象的过程。

## 原理

### 简单说说 AOP 的设计:

1. 每个 Bean 都会被 JDK 或者 Cglib 代理。取决于是否有接口。
2. 每个 Bean 会有多个“方法拦截器”。注意：拦截器分为两层，外层由 Spring 内核控制流程，内层拦截器是用户设置，也就是 AOP。
3. 当代理方法被调用时，先经过外层拦截器，外层拦截器根据方法的各种信息判断该方法应该执行哪些“内层拦截器”。内层拦截器的设计就是职责连的设计。

### 流程

![](https://raw.githubusercontent.com/YangAnLin/images/master/20200831134706.jpeg)

代理的创建（按步骤）：

- 首先，需要创建代理工厂，代理工厂需要 3 个重要的信息：拦截器数组，目标对象接口数组，目标对象。
- 创建代理工厂时，默认会在拦截器数组尾部再增加一个默认拦截器 —— 用于最终的调用目标方法。
- 当调用 getProxy 方法的时候，会根据接口数量大余 0 条件返回一个代理对象（JDK or  Cglib）。
- 注意：创建代理对象时，同时会创建一个外层拦截器，这个拦截器就是 Spring 内核的拦截器。用于控制整个 AOP 的流程。

代理的调用

- 当对代理对象进行调用时，就会触发外层拦截器。
- 外层拦截器根据代理配置信息，创建内层拦截器链。创建的过程中，会根据表达式判断当前拦截是否匹配这个拦截器。而这个拦截器链设计模式就是职责链模式。
- 当整个链条执行到最后时，就会触发创建代理时那个尾部的默认拦截器，从而调用目标方法。最后返回。

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

# Springboot 启动流程

https://juejin.im/post/6844903669998026759



![](https://raw.githubusercontent.com/YangAnLin/images/master/20200831142934.webp)

通过 `SpringFactoriesLoader` 加载 `META-INF/spring.factories` 文件，获取并创建 `SpringApplicationRunListener` 对象

然后由 `SpringApplicationRunListener` 来发出 starting 消息

创建参数，并配置当前 SpringBoot 应用将要使用的 Environment

完成之后，依然由 `SpringApplicationRunListener` 来发出 environmentPrepared 消息

创建 `ApplicationContext`

初始化 `ApplicationContext`，并设置 Environment，加载相关配置等

由 `SpringApplicationRunListener` 来发出 `contextPrepared` 消息，告知SpringBoot 应用使用的 `ApplicationContext` 已准备OK

将各种 beans 装载入 `ApplicationContext`，继续由 `SpringApplicationRunListener` 来发出 contextLoaded 消息，告知 SpringBoot 应用使用的 `ApplicationContext` 已装填OK

refresh ApplicationContext，完成IoC容器可用的最后一步

由 `SpringApplicationRunListener` 来发出 started 消息

完成最终的程序的启动

由 `SpringApplicationRunListener` 来发出 running 消息，告知程序已运行起来了





# 静态变量注入

```properties
application.properties中配置下面两个配置项
ccb.ip.address=10.25.177.31
ccb.ip.port=1600
下面问题代码中读取不到application.properties配置文件中的配置
```

```java
@Component
public class BISFrontFileUtil {
    private static Logger logger = LoggerFactory.getLogger(BISFrontFileUtil.class);

    private static String CCBIPADDRESS;

    private static int CCBIPPORT;

    @Value("${ccb.ip.address}")
    public void setCCBIPADDRESS(String cCBIPADDRESS) {
        CCBIPADDRESS = cCBIPADDRESS;
    }

    @Value("${ccb.ip.port}")
    public void setCCBIPPORT(int cCBIPPORT) {
        CCBIPPORT = cCBIPPORT;
    }
}
```

注意:

1. 修正代码中的@Component不可丢掉了
2. set方法要是非静态的