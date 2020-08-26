



# Tomcat的使用

## 下载

![image-20200820141629483](https://image.yanganlin.com/20200820162630.png)

## 使用

解压之后,进入`BIN`目录

```shell
# 启动
打开startup.bat文件

# 停止
打开shutdown.bat文件

# 访问
http://localhost:8000
```

![image-20200820141844196](https://raw.githubusercontent.com/YangAnLin/images/master/20200825204236.png)

## 文件目录结构

| 目录    | 目录下文件                    | 说明                                                         |
| ------- | ----------------------------- | ------------------------------------------------------------ |
| bin     | /                             | 存放Tomcat的启动,停止等批处理脚本文件                        |
|         | startup.bat,<br />startup.sh  | 用于win和linux下的启动脚本                                   |
|         | shutdown.bat<br />shutdown.sh | win和linux下的停止脚本                                       |
| conf    | /                             | Tomcat的相关配置文件                                         |
|         | Catalina                      | 用于存放针对每个虚拟机的Context配置                          |
|         | context.xml                   | 所有web应用均需加载的Context配置,如果改web应用指定了自己的context.xml,改文件被覆盖 |
|         | catalina.properties           | Tomcat的环境变量配置                                         |
|         | catalina.policy               | Tomcat运行的安全策略配置                                     |
|         | logging.properties            | 日志配置文件,可以通过改文件修改Tomcat的日志级别以及日记路径等 |
|         | server.xml                    | 核心配置文件                                                 |
|         | tomcat-users.xml              | 定义Tomcat默认的用户和角色映射信息配置                       |
|         | web.xml                       | Tomcat中所有应用默认的部署描述文件,主要定义了基础的Servlet和MIME映射 |
| lib     | /                             | 依赖包                                                       |
| logs    | /                             | 默认的日志存放目录                                           |
| webapps | /                             | 默认的web应用部署目录                                        |
| work    | /                             | web应用JSP代码生成和编译的临时目录                           |

# 源码安装运行

## 下载

![image-20200820142736709](https://raw.githubusercontent.com/YangAnLin/images/master/20200825204247.png)

## 安装

1.解压zip压缩包

2.进入解压目录，并创建一个目录，命名为home ， 并将conf、webapps目录移入home 目录中

3.创建pom.xml,引入tomcat依赖包

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>Tomcat8.5</artifactId>
    <name>Tomcat8.5</name>
    <version>8.5</version>

    <build>
        <finalName>Tomcat8.5</finalName>
        <sourceDirectory>java</sourceDirectory>
        <testSourceDirectory>test</testSourceDirectory>
        <resources>
            <resource>
                <directory>java</directory>
            </resource>
        </resources>
        <testResources>
            <testResource>
                <directory>test</directory>
            </testResource>
        </testResources>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.easymock</groupId>
            <artifactId>easymock</artifactId>
            <version>3.4</version>
        </dependency>
        <dependency>
            <groupId>ant</groupId>
            <artifactId>ant</artifactId>
            <version>1.7.0</version>
        </dependency>
        <dependency>
            <groupId>wsdl4j</groupId>
            <artifactId>wsdl4j</artifactId>
            <version>1.6.2</version>
        </dependency>
        <dependency>
            <groupId>javax.xml</groupId>
            <artifactId>jaxrpc</artifactId>
            <version>1.1</version>
        </dependency>
        <dependency>
            <groupId>org.eclipse.jdt.core.compiler</groupId>
            <artifactId>ecj</artifactId>
            <version>4.5.1</version>
        </dependency>

    </dependencies>
</project>
```

4.使用IDEA,导入该工程

![image-20200820143114385](https://image.yanganlin.com/20200820162740.png)

5.配置启动环境

![image-20200820143636964](https://image.yanganlin.com/20200820163120.png)

```shell
# 下面几个配置中的home,是指刚才创建的home文件夹
-Dcatalina.home=home
-Dcatalina.base=home
-Djava.endorsed.dirs=home/endorsed
-Djava.io.tmpdir=home/temp
-Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager
-Djava.util.logging.config.file=home/conf/logging.properties
```

6.如果编译不通过,就注释掉,本文中的Tomcat源码util.TestCookieFilter类会报错，将其注释即可

7.启动,访问http://localhost:8080

![image-20200820143409104](https://image.yanganlin.com/20200820163139.png)

直接启动 org.apache.catalina.startup.Bootstrap的时候没有加载JasperInitializer，从而无法编译JSP。

解决办法是在tomcat的源码org.apache.catalina.startup.ContextConfig中的configureStart函数中手动将JSP解析器初始化：

```java
protected synchronized void configureStart() {
        // Called from StandardContext.start()

        if (log.isDebugEnabled()) {
            log.debug(sm.getString("contextConfig.start"));
        }

        if (log.isDebugEnabled()) {
            log.debug(sm.getString("contextConfig.xmlSettings",
                    context.getName(),
                    Boolean.valueOf(context.getXmlValidation()),
                    Boolean.valueOf(context.getXmlNamespaceAware())));
        }

        webConfig();

    	// 这一行就是新增加的
        context.addServletContainerInitializer(new JasperInitializer(), null);
        
        if (!context.getIgnoreAnnotations()) {
            applicationAnnotationsConfig();
        }
        if (ok) {
            validateSecurityRoles();
        }

        // Configure an authenticator if we need one
        if (ok) {
            authenticatorConfig();
        }

        // Dump the contents of this pipeline if requested
        if (log.isDebugEnabled()) {
            log.debug("Pipeline Configuration:");
            Pipeline pipeline = context.getPipeline();
            Valve valves[] = null;
            if (pipeline != null) {
                valves = pipeline.getValves();
            }
            if (valves != null) {
                for (int i = 0; i < valves.length; i++) {
                    log.debug("  " + valves[i].getClass().getName());
                }
            }
            log.debug("======================");
        }

        // Make our application available if no problems were encountered
        if (ok) {
            context.setConfigured(true);
        } else {
            log.error(sm.getString("contextConfig.unavailable"));
            context.setConfigured(false);
        }

    }
```

8.再启动,就能看到首页了

# Tomcat架构

## Http请求处理

![image-20200814053746311](https://image.yanganlin.com/20200820165528.png)

图1,表示HTTP服务器直接调用具体业务类，它们是紧耦合的。
图2,HTTP服务器不直接调用业务类，而是把请求交给容器来处理，容器通过Servlet接口调用业务类。

因此Servlet接口和Servlet容器的出现，达到了HTTP服务器与业务类解耦的目的,Tomcat按照Servlet规范的要求实现了Servlet容器，同时它们也具有HTTP服务器的功能。

作为Java程序员，如果我们要实现新的业务功能，只需要实现一个Servlet，并把它注册到Tomcat（Servlet容器）中，剩下的事情就由Tomcat帮我们处理了。

## Servlet容器工作流程

为了解耦，HTTP服务器不直接调用Servlet，而是把请求交给Servlet容器来处理

![image-20200819094413866](https://image.yanganlin.com/20200820165541.png)

## Tomcat整体架构

我们知道如果要设计一个系统，首先是要了解需求，我们已经了解了Tomcat要实现两个核心功能：
1） 处理Socket连接，负责网络字节流与Request和Response对象的转化。
2） 加载和管理Servlet，以及具体处理Request请求。
因此Tomcat设计了两个核心组件连接器（Connector）和容器（Container）来分别做这两件事情。连接器负责对外交流，容器负责内部处理。

![image-20200819094629166](https://image.yanganlin.com/20200820162822.png)

### 连接器 - Coyote

#### 架构介绍

Coyote 是Tomcat的连接器框架的名称 , 是Tomcat服务器提供的供客户端访问的外部接口。

客户端通过Coyote与服务器建立连接、发送请求并接受响应 。

Coyote 封装了底层的网络通信（Socket 请求及响应处理），为Catalina 容器提供了统一的接口，使Catalina 容器与具体的请求协议及IO操作方式完全解耦。

Coyote 将Socket 输入转换封装为 Request 对象，交由Catalina 容器进行处理，处理请求完成后, Catalina 通过Coyote 提供的Response 对象将结果写入输出流 。

Coyote 作为独立的模块，只负责具体协议和IO的相关操作， 与Servlet 规范实现没有直接关系，因此即便是 Request 和 Response 对象也并未实现Servlet规范对应的接口， 而是在Catalina 中将他们进一步封装为ServletRequest 和 ServletResponse 

![image-20200819095016265](https://image.yanganlin.com/20200820162916.png)

#### IO 模型与协议

在Coyote中 ， Tomcat支持的多种I/O模型和应用层协议，具体包含哪些IO模型和应用层协议，请看下表：
Tomcat 支持的IO模型（自8.5/9.0 版本起，Tomcat 移除了 对 BIO 的支持）：

| IO 模型 | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| NIO     | 非阻塞I/O，采用Java NIO类库实现                              |
| NIO2    | 异步I/O，采用JDK 7最新的NIO2类库实现。                       |
| APR     | 采用Apache可移植运行库实现，是C/C++编写的本地库。如果选择该方<br/>案，需要单独安装APR库。 |

Tomcat 支持的应用层协议 ：
| IO 模型  | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| HTTP/1.1 | 这是大部分Web应用采用的访问协议。                            |
| AJP      | 用于和Web服务器集成（如Apache），以实现对静态资源的优化以及<br/>集群部署，当前支持AJP/1.3。 |
| HTTP/2   | HTTP 2.0大幅度的提升了Web性能。下一代HTTP协议 ， 自8.5以及9.0<br/>版本之后支持。 |

在 8.0 之前 ， Tomcat 默认采用的I/O方式为 BIO ， 之后改为 NIO。 无论 NIO、NIO2还是 APR， 在性能方面均优于以往的BIO。 如果采用APR， 甚至可以达到 Apache HTTPServer 的影响性能。

#### 连接器组件

![image-20200819095912251](https://image.yanganlin.com/20200820162948.png)

连接器中的各个组件的作用如下：

##### EndPoint

1） EndPoint ： Coyote 通信端点，即通信监听的接口，是具体Socket接收和发送处理器，是对传输层的抽象，因此EndPoint用来实现TCP/IP协议的。

2） Tomcat 并没有EndPoint 接口，而是提供了一个抽象类AbstractEndpoint ， 里面定义了两个内部类：Acceptor和SocketProcessor。

* Acceptor用于监听Socket连接请求。

* SocketProcessor用于处理接收到的Socket请求，它实现Runnable接口，在Run方法里调用协议处理组件Processor进行处理。为了提高处理能力，SocketProcessor被提交到线程池来执行。而这个线程池叫作执行器(Executor)

##### Processor

Processor ： Coyote 协议处理接口 ，Processor接收来自EndPoint的Socket，读取字节流解析成Tomcat Request和Response对象，并通过Adapter将其提交到容器处理

##### ProtocolHandler

ProtocolHandler ： Coyote 协议接口， 通过Endpoint 和 Processor ， 实现针对具体协议的处理能力。

Tomcat 按照协议和I/O 提供了6个实现类 ： AjpNioProtocol ，AjpAprProtocol， AjpNio2Protocol ， Http11NioProtocol ，Http11Nio2Protocol ，Http11AprProtocol。

我们在配置tomcat/conf/server.xml 时 ， 至少要指定具体的ProtocolHandler , 当然也可以指定协议名称 ， 如 ： HTTP/1.1 

##### Adapter

由于协议不同，客户端发过来的请求信息也不尽相同，Tomcat定义了自己的Request类来“存放”这些请求信息。ProtocolHandler接口负责解析请求并生成Tomcat Request类。但是这个Request对象不是标准的ServletRequest，也就意味着，不能用Tomcat Request作为参数来调用容器。

Tomcat设计者的解决方案是引入CoyoteAdapter，这是适配器模式的经典运用，连接器调用CoyoteAdapter的Sevice方法，传入的是TomcatRequest对象，CoyoteAdapter负责将Tomcat Request转成ServletRequest，再调用容器的Service方法

### Container结构

Tomcat设计了4种容器，分别是Engine、Host、Context和Wrapper。这4种容器不是平行关系，而是父子关系。， Tomcat通过一种分层的架构，使得Servlet容器具有很好的灵活性

![image-20200819101901372](https://image.yanganlin.com/20200820163005.png)

| 容器    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| Engine  | 标识整个Catalina的servlet引擎,用来管理多个虚拟站点,一个Service只能有一个Engine,但是可以有多个Host |
| Host    | 一个虚拟主机,或者说一个站点,可以给Tomcat配置多个虚拟主机地址,而一个虚拟主机下可包含多个Context |
| Context | 表示一个Web应用程序,一个Context包含多个Wrapper               |
| Wrapper | 一个Wrapper标识一个Servlet                                   |

```xml
<Server>
  <Service>
    <Connector/>
    <Connector/>
    <Engine>
      <Host></Host>
    </Engine>
  </Service>
</Server>
```

所有容器组件都实现了Container接口，因此组合模式可以使得用户对单容器对象和组合容器对象的使用具有一致性。这里单容器对象指的是最底层的Wrapper，组合容器对象指的是上面的Context、Host或者Engine。

![Container](https://image.yanganlin.com/20200820163015.png)

# 启动流程

![image-20200820151909025](https://image.yanganlin.com/20200820163050.png)

1.启动tomcat ， 需要调用 bin/startup.bat (在linux 目录下 , 需要调用 bin/startup.sh) 在startup.bat 脚本中, 调用了catalina.bat。

2.在catalina.bat 脚本文件中，调用了BootStrap 中的main方法。

3.在BootStrap 的main 方法中调用了 init 方法 ， 来创建Catalina 及 初始化类加载器。

4.在BootStrap 的main 方法中调用了 load 方法 ， 在其中又调用了Catalina的load方法。

5.在Catalina 的load 方法中 , 需要进行一些初始化的工作, 并需要构造Digester 对象, 用于解析 XML。
6.然后在调用后续组件的初始化操作 。。。加载Tomcat的配置文件，初始化容器组件 ，监听对应的端口号， 准备接受客户端请求

# 源码

## 初始化init()

Bootstrap

```java
public static void main(String args[]) {

        if (daemon == null) {
            // Don't set daemon until init() has completed
            Bootstrap bootstrap = new Bootstrap();
            try {
                // 初始化ClassLoader
                bootstrap.init();
            } catch (Throwable t) {
                handleThrowable(t);
                t.printStackTrace();
                return;
            }
            daemon = bootstrap;
        } else {
            // When running as a service the call to stop will be on a new
            // thread so make sure the correct class loader is used to prevent
            // a range of class not found exceptions.
            Thread.currentThread().setContextClassLoader(daemon.catalinaLoader);
        }

        try {
            String command = "start";
            if (args.length > 0) {
                command = args[args.length - 1];
            }

            if (command.equals("startd")) {
                args[args.length - 1] = "start";
                //  用反射,创建Catalina对象
                daemon.load(args);
                daemon.start();
            } else if (command.equals("stopd")) {
                args[args.length - 1] = "stop";
                daemon.stop();
            } else if (command.equals("start")) {
                daemon.setAwait(true);
                daemon.load(args);
                daemon.start();
                if (null == daemon.getServer()) {
                    System.exit(1);
                }
            } else if (command.equals("stop")) {
                daemon.stopServer(args);
            } else if (command.equals("configtest")) {
                daemon.load(args);
                if (null == daemon.getServer()) {
                    System.exit(1);
                }
                System.exit(0);
            } else {
                log.warn("Bootstrap: command \"" + command + "\" does not exist.");
            }
        } catch (Throwable t) {
            // Unwrap the Exception for clearer error reporting
            if (t instanceof InvocationTargetException &&
                    t.getCause() != null) {
                t = t.getCause();
            }
            handleThrowable(t);
            t.printStackTrace();
            System.exit(1);
        }

    }
```

Catalina的load()

```java
/*
     * Load using arguments
     */
public void load(String args[]) {

    try {
        if (arguments(args)) {
            load();
        }
    } catch (Exception e) {
        e.printStackTrace(System.out);
    }
}

/**
     * Start a new server instance.
     */
public void load() {

    if (loaded) {
        return;
    }
    loaded = true;

    long t1 = System.nanoTime();

    initDirs();

    // Before digester - it may be needed
    initNaming();

    // Create and execute our Digester
    Digester digester = createStartDigester();

    InputSource inputSource = null;
    InputStream inputStream = null;
    File file = null;
    try {
        try {
            file = configFile();
            inputStream = new FileInputStream(file);
            inputSource = new InputSource(file.toURI().toURL().toString());
        } catch (Exception e) {
            if (log.isDebugEnabled()) {
                log.debug(sm.getString("catalina.configFail", file), e);
            }
        }
        if (inputStream == null) {
            try {
                inputStream = getClass().getClassLoader()
                    .getResourceAsStream(getConfigFile());
                inputSource = new InputSource
                    (getClass().getClassLoader()
                     .getResource(getConfigFile()).toString());
            } catch (Exception e) {
                if (log.isDebugEnabled()) {
                    log.debug(sm.getString("catalina.configFail",
                                           getConfigFile()), e);
                }
            }
        }

        // This should be included in catalina.jar
        // Alternative: don't bother with xml, just create it manually.
        if (inputStream == null) {
            try {
                inputStream = getClass().getClassLoader()
                    .getResourceAsStream("server-embed.xml");
                inputSource = new InputSource
                    (getClass().getClassLoader()
                     .getResource("server-embed.xml").toString());
            } catch (Exception e) {
                if (log.isDebugEnabled()) {
                    log.debug(sm.getString("catalina.configFail",
                                           "server-embed.xml"), e);
                }
            }
        }


    // 省略很多代码

    getServer().setCatalina(this);
    getServer().setCatalinaHome(Bootstrap.getCatalinaHomeFile());
    getServer().setCatalinaBase(Bootstrap.getCatalinaBaseFile());

    // Stream redirection
    initStreams();

    // Start the new server
    try {
        // 调用server的init方法
        getServer().init();
    } catch (LifecycleException e) {
        if (Boolean.getBoolean("org.apache.catalina.startup.EXIT_ON_INIT_FAILURE")) {
            throw new java.lang.Error(e);
        } else {
            log.error("Catalina.start", e);
        }
    }

    long t2 = System.nanoTime();
    if(log.isInfoEnabled()) {
        log.info("Initialization processed in " + ((t2 - t1) / 1000000) + " ms");
    }
}

```

LifecycleBase,因为用了模板设计模式,这里都是各个实现类的公共方法

```java
    @Override
    public final synchronized void init() throws LifecycleException {
        if (!state.equals(LifecycleState.NEW)) {
            invalidTransition(Lifecycle.BEFORE_INIT_EVENT);
        }

        try {
            setStateInternal(LifecycleState.INITIALIZING, null, false);
            // 调用各个实现类的initInternal()
            initInternal();
            setStateInternal(LifecycleState.INITIALIZED, null, false);
        } catch (Throwable t) {
            ExceptionUtils.handleThrowable(t);
            setStateInternal(LifecycleState.FAILED, null, false);
            throw new LifecycleException(
                    sm.getString("lifecycleBase.initFail",toString()), t);
        }
    }
```

StandardServer是Server的实现类

```java
@Override
    protected void initInternal() throws LifecycleException {

        super.initInternal();

        // 省略很多代码
        
        // Initialize our defined Services
        for (int i = 0; i < services.length; i++) {
            // 调用service的init方法,这里看到,一个server可以有多个service
            services[i].init();
        }
```

然后还是会调用LifecycleBase的initInternal()方法,下面就不再写了,直接到实现类

StandardService

这里会有三个组件会被初始化

首先是Engine组件

然后是Executor(线程池)组件

最后是Connector组件

```java
@Override
    protected void initInternal() throws LifecycleException {

        super.initInternal();

        if (engine != null) {
            // 初始化Engine组件
            engine.init();
        }

        // Initialize any Executors
        for (Executor executor : findExecutors()) {
            if (executor instanceof JmxEnabled) {
                ((JmxEnabled) executor).setDomain(getDomain());
            }
            // 初始化Executor(线程池)组件
            executor.init();
        }

        // Initialize mapper listener
        mapperListener.init();

        // Initialize our defined Connectors
        synchronized (connectorsLock) {
            for (Connector connector : connectors) {
                try {
                    // Connector组件
                    connector.init();
                } catch (Exception e) {
                    String message = sm.getString(
                            "standardService.connector.initFailed", connector);
                    log.error(message, e);

                    if (Boolean.getBoolean("org.apache.catalina.startup.EXIT_ON_INIT_FAILURE"))
                        throw new LifecycleException(message);
                }
            }
        }
    }
```

先看初始化Engine组件

```java
@Override
protected void initInternal() throws LifecycleException {
    // Ensure that a Realm is present before any attempt is made to start
    // one. This will create the default NullRealm if necessary.
    getRealm();
    super.initInternal();
}
```

到这边 Tomcat 的 init 流程已经 OK 了, 至于 StandardHost, 则在 StandardHost.start() 方法触发的时候先调用其 initInternal, 再调用其 startInternal()

初始化线程池就不看了

最后看初始化Connector组件,Connector.java

```java
@Override
    protected void initInternal() throws LifecycleException {

        super.initInternal();

        // 创建适配器
        adapter = new CoyoteAdapter(this);
        protocolHandler.setAdapter(adapter);

        // Make sure parseBodyMethodsSet has a default
        if (null == parseBodyMethodsSet) {
            setParseBodyMethods(getParseBodyMethods());
        }

        if (protocolHandler.isAprRequired() && !AprLifecycleListener.isAprAvailable()) {
            throw new LifecycleException(sm.getString("coyoteConnector.protocolHandlerNoApr",
                    getProtocolHandlerClassName()));
        }
        if (AprLifecycleListener.isAprAvailable() && AprLifecycleListener.getUseOpenSSL() &&
                protocolHandler instanceof AbstractHttp11JsseProtocol) {
            AbstractHttp11JsseProtocol<?> jsseProtocolHandler =
                    (AbstractHttp11JsseProtocol<?>) protocolHandler;
            if (jsseProtocolHandler.isSSLEnabled() &&
                    jsseProtocolHandler.getSslImplementationName() == null) {
                // OpenSSL is compatible with the JSSE configuration, so use it if APR is available
                jsseProtocolHandler.setSslImplementationName(OpenSSLImplementation.class.getName());
            }
        }

        try {
            // 初始化protocolHandler
            protocolHandler.init();
        } catch (Exception e) {
            throw new LifecycleException(
                    sm.getString("coyoteConnector.protocolHandlerInitializationFailed"), e);
        }
    }
```

AbstractHttp11Protocol

```java
    @Override
    public void init() throws Exception {
        for (UpgradeProtocol upgradeProtocol : upgradeProtocols) {
            configureUpgradeProtocol(upgradeProtocol);
        }
        // 调用父类-AbstractProtocol的init方法
        super.init();
    }
```

AbstractProtocol

```java
@Override
public void init() throws Exception {
    if (getLog().isInfoEnabled()) {
        getLog().info(sm.getString("abstractProtocolHandler.init", getName()));
    }

    if (oname == null) {
        // Component not pre-registered so register it
        oname = createObjectName();
        if (oname != null) {
            Registry.getRegistry(null, null).registerComponent(this, oname, null);
        }
    }


    // 省略很多代码

    String endpointName = getName();
    endpoint.setName(endpointName.substring(1, endpointName.length()-1));
    endpoint.setDomain(domain);

    // 创建endpoint
    endpoint.init();
}
```

AbstractJsseEndpoint

```java
@Override
public void init() throws Exception {
    testServerCipherSuitesOrderSupport();
    // 调用父类AbstractEndpoint的init方法
    super.init();
}
```

AbstractEndpoint

```java
public void init() throws Exception {
    if (bindOnInit) {
        // 熟悉的代码,socket的绑定
        bind();
        bindState = BindState.BOUND_ON_INIT;
    }

    // 省略很多代码
}
```

NioEndpoint

```java
@Override
public void bind() throws Exception {

    if (!getUseInheritedChannel()) {
        // socker绑定端口
        serverSock = ServerSocketChannel.open();
        socketProperties.setProperties(serverSock.socket());
        InetSocketAddress addr = (getAddress()!=null?new InetSocketAddress(getAddress(),getPort()):new InetSocketAddress(getPort()));
        serverSock.socket().bind(addr,getAcceptCount());
    } else {
        // Retrieve the channel provided by the OS
        Channel ic = System.inheritedChannel();
        if (ic instanceof ServerSocketChannel) {
            serverSock = (ServerSocketChannel) ic;
        }
        if (serverSock == null) {
            throw new IllegalArgumentException(sm.getString("endpoint.init.bind.inherited"));
        }
    }
    serverSock.configureBlocking(true); //mimic APR behavior

    // Initialize thread count defaults for acceptor, poller
    if (acceptorThreadCount == 0) {
        // FIXME: Doesn't seem to work that well with multiple accept threads
        acceptorThreadCount = 1;
    }
    if (pollerThreadCount <= 0) {
        //minimum one poller thread
        pollerThreadCount = 1;
    }
    setStopLatch(new CountDownLatch(pollerThreadCount));

    // Initialize SSL if needed
    initialiseSsl();

    selectorPool.open();
}
```

## 初始化start()

BootStrap

```java
public static void main(String args[]) {

        if (daemon == null) {
            // Don't set daemon until init() has completed
            Bootstrap bootstrap = new Bootstrap();
            try {
                bootstrap.init();
            } catch (Throwable t) {
                handleThrowable(t);
                t.printStackTrace();
                return;
            }
            daemon = bootstrap;
        } else {
            // When running as a service the call to stop will be on a new
            // thread so make sure the correct class loader is used to prevent
            // a range of class not found exceptions.
            Thread.currentThread().setContextClassLoader(daemon.catalinaLoader);
        }

        try {
            String command = "start";
            if (args.length > 0) {
                command = args[args.length - 1];
            }

            if (command.equals("startd")) {
                args[args.length - 1] = "start";
                daemon.load(args);
                daemon.start();
            } else if (command.equals("stopd")) {
                args[args.length - 1] = "stop";
                daemon.stop();
            } else if (command.equals("start")) {
                daemon.setAwait(true);
                // init流程
                daemon.load(args);
                // start流程
                daemon.start();
                if (null == daemon.getServer()) {
                    System.exit(1);
                }
            } else if (command.equals("stop")) {
                daemon.stopServer(args);
            } else if (command.equals("configtest")) {
                daemon.load(args);
                if (null == daemon.getServer()) {
                    System.exit(1);
                }
                System.exit(0);
            } else {
                log.warn("Bootstrap: command \"" + command + "\" does not exist.");
            }
        } catch (Throwable t) {
            // Unwrap the Exception for clearer error reporting
            if (t instanceof InvocationTargetException &&
                    t.getCause() != null) {
                t = t.getCause();
            }
            handleThrowable(t);
            t.printStackTrace();
            System.exit(1);
        }
    }


    public void start()
        throws Exception {
        if( catalinaDaemon==null ) init();

        // 通过反射,调用Catalina的start方法
        Method method = catalinaDaemon.getClass().getMethod("start", (Class [] )null);
        method.invoke(catalinaDaemon, (Object [])null);

    }
```

Catalina

```java
public void start() {

    if (getServer() == null) {
        load();
    }

    if (getServer() == null) {
        log.fatal("Cannot start server. Server instance is not configured.");
        return;
    }

    long t1 = System.nanoTime();

    // Start the new server
    try {
        // 调用这里
        getServer().start();
    } catch (LifecycleException e) {
        log.fatal(sm.getString("catalina.serverStartFail"), e);
        try {
            getServer().destroy();
        } catch (LifecycleException e1) {
            log.debug("destroy() failed for failed Server ", e1);
        }
        return;
    }

    // 省略代码

}
```

LifecycleBase,这里用的也是模板方法

```java
public final synchronized void start() throws LifecycleException {

    // 省略代码

    try {
        setStateInternal(LifecycleState.STARTING_PREP, null, false);
        // 调用server的start方法
        startInternal();
        if (state.equals(LifecycleState.FAILED)) {
            // This is a 'controlled' failure. The component put itself into the
            // FAILED state so call stop() to complete the clean-up.
            stop();
        } else if (!state.equals(LifecycleState.STARTING)) {
            // Shouldn't be necessary but acts as a check that sub-classes are
            // doing what they are supposed to.
            invalidTransition(Lifecycle.AFTER_START_EVENT);
        } else {
            setStateInternal(LifecycleState.STARTED, null, false);
        }
    } catch (Throwable t) {
        // This is an 'uncontrolled' failure so put the component into the
        // FAILED state and throw an exception.
        ExceptionUtils.handleThrowable(t);
        setStateInternal(LifecycleState.FAILED, null, false);
        throw new LifecycleException(sm.getString("lifecycleBase.startFail", toString()), t);
    }
}
```

StandardServer

```java
@Override
protected void startInternal() throws LifecycleException {

    fireLifecycleEvent(CONFIGURE_START_EVENT, null);
    setState(LifecycleState.STARTING);

    globalNamingResources.start();

    // Start our defined Services
    synchronized (servicesLock) {
        for (int i = 0; i < services.length; i++) {
            // 调用service的start方法
            services[i].start();
        }
    }
}
```

然后又会调用LifecycleBase的start方法,这里就不重复写了

StandardService

```java
@Override
protected void startInternal() throws LifecycleException {

   // 省略代码

    // Start our defined Container first
    if (engine != null) {
        synchronized (engine) {
            // 调用engine的start方法
            engine.start();
        }
    }

    synchronized (executors) {
        for (Executor executor: executors) {
            // 启动线程池
            executor.start();
        }
    }

    mapperListener.start();

    // Start our defined Connectors second
    synchronized (connectorsLock) {
        for (Connector connector: connectors) {
            try {
                // If it has already failed, don't try and start it
                if (connector.getState() != LifecycleState.FAILED) {
                    // 调用连接器的start方法
                    connector.start();
                }
            } catch (Exception e) {
                log.error(sm.getString(
                    "standardService.connector.startFailed",
                    connector), e);
            }
        }
    }
}
```

看先engine.start();

StandardEngine

```java
@Override
protected synchronized void startInternal() throws LifecycleException {

    // Log our server identification information
    if(log.isInfoEnabled())
        log.info( "Starting Servlet Engine: " + ServerInfo.getServerInfo());

    // Standard container startup
    super.startInternal();
}
```

StandardHost

```java
@Override
protected synchronized void startInternal() throws LifecycleException {

    // 省略代码
    
    // 调用context的start方法
    super.startInternal();
}
```

StandardContext

```java
@Override
protected synchronized void startInternal() throws LifecycleException {
    // 调用到这里,这里的start就结束了
}
```

再看connector.start();

Connector

```java
@Override
protected void startInternal() throws LifecycleException {

    // Validate settings before starting
    if (getPort() < 0) {
        throw new LifecycleException(sm.getString(
            "coyoteConnector.invalidPort", Integer.valueOf(getPort())));
    }

    setState(LifecycleState.STARTING);

    try {
        // 调用AbstractProtocol的start方法
        protocolHandler.start();
    } catch (Exception e) {
        throw new LifecycleException(
            sm.getString("coyoteConnector.protocolHandlerStartFailed"), e);
    }
}
```

AbstractProtocol

```java
@Override
public void start() throws Exception {
    if (getLog().isInfoEnabled()) {
        getLog().info(sm.getString("abstractProtocolHandler.start", getName()));
    }

    // 调用 AbstractEndpoint的start方法
    endpoint.start();

    // Start async timeout thread
    asyncTimeout = new AsyncTimeout();
    Thread timeoutThread = new Thread(asyncTimeout, getNameInternal() + "-AsyncTimeout");
    int priority = endpoint.getThreadPriority();
    if (priority < Thread.MIN_PRIORITY || priority > Thread.MAX_PRIORITY) {
        priority = Thread.NORM_PRIORITY;
    }
    timeoutThread.setPriority(priority);
    timeoutThread.setDaemon(true);
    timeoutThread.start();
}
```

AbstractEndpoint

```java
public final void start() throws Exception {
    if (bindState == BindState.UNBOUND) {
        // 绑定socket
        bind();
        bindState = BindState.BOUND_ON_START;
    }
    // 请求下一步
    startInternal();
}
```

NioEndpoint

```java
@Override
public void startInternal() throws Exception {

    if (!running) {
        running = true;
        paused = false;

        processorCache = new SynchronizedStack<>(SynchronizedStack.DEFAULT_SIZE,
                                                 socketProperties.getProcessorCache());
        eventCache = new SynchronizedStack<>(SynchronizedStack.DEFAULT_SIZE,
                                             socketProperties.getEventCache());
        nioChannels = new SynchronizedStack<>(SynchronizedStack.DEFAULT_SIZE,
                                              socketProperties.getBufferPool());

        // Create worker collection
        if ( getExecutor() == null ) {
            createExecutor();
        }

        initializeConnectionLatch();

        // Start poller threads
        pollers = new Poller[getPollerThreadCount()];
        for (int i=0; i<pollers.length; i++) {
            pollers[i] = new Poller();
            Thread pollerThread = new Thread(pollers[i], getName() + "-ClientPoller-"+i);
            pollerThread.setPriority(threadPriority);
            pollerThread.setDaemon(true);
            pollerThread.start();
        }

        // 启动socket
        startAcceptorThreads();
    }
}


protected final void startAcceptorThreads() {
    int count = getAcceptorThreadCount();
    acceptors = new Acceptor[count];

    for (int i = 0; i < count; i++) {
        // 创建接收器
        acceptors[i] = createAcceptor();
        String threadName = getName() + "-Acceptor-" + i;
        acceptors[i].setThreadName(threadName);
        Thread t = new Thread(acceptors[i], threadName);
        t.setPriority(getAcceptorThreadPriority());
        t.setDaemon(getDaemon());
        // 启动,调用run方法
        t.start();
    }
}



protected class Acceptor extends AbstractEndpoint.Acceptor {

    @Override
    public void run() {

        int errorDelay = 0;

        // Loop until we receive a shutdown command
        while (running) {

            // Loop if endpoint is paused
            while (paused && running) {
                state = AcceptorState.PAUSED;
                try {
                    Thread.sleep(50);
                } catch (InterruptedException e) {
                    // Ignore
                }
            }

            if (!running) {
                break;
            }
            state = AcceptorState.RUNNING;

            try {
                //if we have reached max connections, wait
                countUpOrAwaitConnection();

                SocketChannel socket = null;
                try {
                    // Accept the next incoming connection from the server
                    // socket
                    // 这里是重点
                    socket = serverSock.accept();
                } catch (IOException ioe) {
                    // We didn't get a socket
                    countDownConnection();
                    if (running) {
                        // Introduce delay if necessary
                        errorDelay = handleExceptionWithDelay(errorDelay);
                        // re-throw
                        throw ioe;
                    } else {
                        break;
                    }
                }
                // Successful accept, reset the error delay
                errorDelay = 0;

                // Configure the socket
                if (running && !paused) {
                    // setSocketOptions() will hand the socket off to
                    // an appropriate processor if successful
                    if (!setSocketOptions(socket)) {
                        closeSocket(socket);
                    }
                } else {
                    closeSocket(socket);
                }
            } catch (Throwable t) {
                ExceptionUtils.handleThrowable(t);
                log.error(sm.getString("endpoint.accept.fail"), t);
            }
        }
        state = AcceptorState.ENDED;
    }



    // 省略代码
}
```







## 请求处理

先创建个web项目

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>bbsServlet</servlet-name>
        <servlet-class>cn.itcast.web.BBSServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>bbsServlet</servlet-name>
        <url-pattern>/bbs</url-pattern>
    </servlet-mapping>
</web-app>
```

```java
public class BBSServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("====================get===========");
    }


    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("====================post===========");
    }
}
```

build下,把生成的代码,放到源码的webapps目录下



NioEndpoint.Acceptor

```java
protected class Acceptor extends AbstractEndpoint.Acceptor {

    @Override
    public void run() {

        int errorDelay = 0;

        // Loop until we receive a shutdown command
        while (running) {


            // 省略代码

            try {
                //if we have reached max connections, wait
                countUpOrAwaitConnection();

                SocketChannel socket = null;
                try {
                    // Accept the next incoming connection from the server
                    // socket
                    // 当有请求的时候,首先在这里打断点,这里会接受到请求
                    socket = serverSock.accept();
                } catch (IOException ioe) {
                    // We didn't get a socket
                    countDownConnection();
                    if (running) {
                        // Introduce delay if necessary
                        errorDelay = handleExceptionWithDelay(errorDelay);
                        // re-throw
                        throw ioe;
                    } else {
                        break;
                    }
                }
                // Successful accept, reset the error delay
                errorDelay = 0;

                // Configure the socket
                if (running && !paused) {
                    // setSocketOptions() will hand the socket off to
                    // an appropriate processor if successful
                    // 放到队列里,所以accept只是用来做接收请求的功能
                    if (!setSocketOptions(socket)) {
                        closeSocket(socket);
                    }
                } else {
                    closeSocket(socket);
                }
            } catch (Throwable t) {
                ExceptionUtils.handleThrowable(t);
                log.error(sm.getString("endpoint.accept.fail"), t);
            }
        }
        state = AcceptorState.ENDED;
    }
    // 省略代码
}
```

org.apache.tomcat.util.net.NioEndpoint#setSocketOptions

```java
protected boolean setSocketOptions(SocketChannel socket) {
        // Process the connection
        try {
            //disable blocking, APR style, we are gonna be polling it
            socket.configureBlocking(false);
            Socket sock = socket.socket();
            socketProperties.setProperties(sock);

            // 构造对象
            NioChannel channel = nioChannels.pop();
            if (channel == null) {
                SocketBufferHandler bufhandler = new SocketBufferHandler(
                        socketProperties.getAppReadBufSize(),
                        socketProperties.getAppWriteBufSize(),
                        socketProperties.getDirectBuffer());
                if (isSSLEnabled()) {
                    channel = new SecureNioChannel(socket, bufhandler, selectorPool, this);
                } else {
                    channel = new NioChannel(socket, bufhandler);
                }
            } else {
                channel.setIOChannel(socket);
                channel.reset();
            }
            // 创建的对象放到队列里
            getPoller0().register(channel);
        } catch (Throwable t) {
            ExceptionUtils.handleThrowable(t);
            try {
                log.error("",t);
            } catch (Throwable tt) {
                ExceptionUtils.handleThrowable(tt);
            }
            // Tell to close the socket
            return false;
        }
        return true;
    }
```

org.apache.tomcat.util.net.NioEndpoint.Poller#register

```java
public void register(final NioChannel socket) {
    socket.setPoller(this);
    NioSocketWrapper ka = new NioSocketWrapper(socket, NioEndpoint.this);
    socket.setSocketWrapper(ka);
    ka.setPoller(this);
    ka.setReadTimeout(getSocketProperties().getSoTimeout());
    ka.setWriteTimeout(getSocketProperties().getSoTimeout());
    ka.setKeepAliveLeft(NioEndpoint.this.getMaxKeepAliveRequests());
    ka.setSecure(isSSLEnabled());
    ka.setReadTimeout(getConnectionTimeout());
    ka.setWriteTimeout(getConnectionTimeout());
    PollerEvent r = eventCache.pop();
    ka.interestOps(SelectionKey.OP_READ);//this is what OP_REGISTER turns into.
    if ( r==null) r = new PollerEvent(socket,ka,OP_REGISTER);
    else r.reset(socket,ka,OP_REGISTER);
    // 加入到队列中
    addEvent(r);
}
```

```java
private final SynchronizedQueue<PollerEvent> events =new SynchronizedQueue<>();
```

这里涉及到NIO编程,也要去学习下

org.apache.tomcat.util.net.NioEndpoint.Poller#run

```java
@Override
public void run() {
    // Loop until destroy() is called
    while (true) {

        // 省略代码
        // 走到这里
        processKey(sk, attachment);

}
    
    
protected void processKey(SelectionKey sk, NioSocketWrapper attachment) {
    try {
        if ( close ) {
            cancelledKey(sk);
        } else if ( sk.isValid() && attachment != null ) {
            if (sk.isReadable() || sk.isWritable() ) {
                if ( attachment.getSendfileData() != null ) {
                    processSendfile(sk,attachment, false);
                } else {
                    unreg(sk, attachment, sk.readyOps());
                    boolean closeSocket = false;
                    // Read goes before write
                    if (sk.isReadable()) {
                        // 这里
                        if (!processSocket(attachment, SocketEvent.OPEN_READ, true)) {
                            closeSocket = true;
                        }
                    }
                    if (!closeSocket && sk.isWritable()) {
                        if (!processSocket(attachment, SocketEvent.OPEN_WRITE, true)) {
                            closeSocket = true;
                        }
                    }
                    if (closeSocket) {
                        cancelledKey(sk);
                    }
                }
            }
        } else {
            //invalid key
            cancelledKey(sk);
        }
    } catch ( CancelledKeyException ckx ) {
        cancelledKey(sk);
    } catch (Throwable t) {
        ExceptionUtils.handleThrowable(t);
        log.error("",t);
    }
}
```

org.apache.tomcat.util.net.AbstractEndpoint#processSocket

```java
public boolean processSocket(SocketWrapperBase<S> socketWrapper,
            SocketEvent event, boolean dispatch) {
        try {
            if (socketWrapper == null) {
                return false;
            }
            SocketProcessorBase<S> sc = processorCache.pop();
            if (sc == null) {
                sc = createSocketProcessor(socketWrapper, event);
            } else {
                sc.reset(socketWrapper, event);
            }
            Executor executor = getExecutor();
            if (dispatch && executor != null) {
                // 给线程池运行,调用SocketProcessorBase的run方法
                executor.execute(sc);
            } else {
                sc.run();
            }
        } catch (RejectedExecutionException ree) {
            getLog().warn(sm.getString("endpoint.executor.fail", socketWrapper) , ree);
            return false;
        } catch (Throwable t) {
            ExceptionUtils.handleThrowable(t);
            // This means we got an OOM or similar creating a thread, or that
            // the pool and its queue are full
            getLog().error(sm.getString("endpoint.process.fail"), t);
            return false;
        }
        return true;
    }
```

org.apache.tomcat.util.net.SocketProcessorBase#run

```java
    @Override
    public final void run() {
        synchronized (socketWrapper) {
          	// 省略代码
            doRun();
        }
    }
```

org.apache.tomcat.util.net.NioEndpoint.SocketProcessor#doRun

```java
@Override
protected void doRun() {
    NioChannel socket = socketWrapper.getSocket();
    SelectionKey key = socket.getIOChannel().keyFor(socket.getPoller().getSelector());

    try {
        int handshake = -1;

        try {
            if (key != null) {
                if (socket.isHandshakeComplete()) {
                    // No TLS handshaking required. Let the handler
                    // process this socket / event combination.
                    handshake = 0;
                } else if (event == SocketEvent.STOP || event == SocketEvent.DISCONNECT ||
                           event == SocketEvent.ERROR) {
                    // Unable to complete the TLS handshake. Treat it as
                    // if the handshake failed.
                    handshake = -1;
                } else {
                    handshake = socket.handshake(key.isReadable(), key.isWritable());
                    // The handshake process reads/writes from/to the
                    // socket. status may therefore be OPEN_WRITE once
                    // the handshake completes. However, the handshake
                    // happens when the socket is opened so the status
                    // must always be OPEN_READ after it completes. It
                    // is OK to always set this as it is only used if
                    // the handshake completes.
                    event = SocketEvent.OPEN_READ;
                }
            }
        } catch (IOException x) {
            handshake = -1;
            if (log.isDebugEnabled()) log.debug("Error during SSL handshake",x);
        } catch (CancelledKeyException ckx) {
            handshake = -1;
        }
        if (handshake == 0) {
            SocketState state = SocketState.OPEN;
            // Process the request from this socket
            if (event == null) {
                state = getHandler().process(socketWrapper, SocketEvent.OPEN_READ);
            } else {
                // 调用这里
                state = getHandler().process(socketWrapper, event);
            }
            if (state == SocketState.CLOSED) {
                close(socket, key);
            }
        } else if (handshake == -1 ) {
            close(socket, key);
        } else if (handshake == SelectionKey.OP_READ){
            socketWrapper.registerReadInterest();
        } else if (handshake == SelectionKey.OP_WRITE){
            socketWrapper.registerWriteInterest();
        }
    } catch (CancelledKeyException cx) {
        socket.getPoller().cancelledKey(key);
    } catch (VirtualMachineError vme) {
        ExceptionUtils.handleThrowable(vme);
    } catch (Throwable t) {
        log.error("", t);
        socket.getPoller().cancelledKey(key);
    } finally {
        socketWrapper = null;
        event = null;
        //return to cache
        if (running && !paused) {
            processorCache.push(this);
        }
    }
}
```

org.apache.coyote.AbstractProtocol.ConnectionHandler#process

```java
@Override
public SocketState process(SocketWrapperBase<S> wrapper, SocketEvent status) {
	// 省略代码
    state = processor.process(wrapper, status);
}
```

org.apache.coyote.AbstractProcessorLight#process

```java
@Override
public SocketState process(SocketWrapperBase<?> socketWrapper, SocketEvent status)
    throws IOException {

    SocketState state = SocketState.CLOSED;
    Iterator<DispatchType> dispatches = null;
    do {
        if (dispatches != null) {
            DispatchType nextDispatch = dispatches.next();
            state = dispatch(nextDispatch.getSocketStatus());
        } else if (status == SocketEvent.DISCONNECT) {
            // Do nothing here, just wait for it to get recycled
        } else if (isAsync() || isUpgrade() || state == SocketState.ASYNC_END) {
            state = dispatch(status);
            if (state == SocketState.OPEN) {
                // There may be pipe-lined data to read. If the data isn't
                // processed now, execution will exit this loop and call
                // release() which will recycle the processor (and input
                // buffer) deleting any pipe-lined data. To avoid this,
                // process it now.
                // 调用servie
                state = service(socketWrapper);
            }
        } else if (status == SocketEvent.OPEN_WRITE) {
            // Extra write event likely after async, ignore
            state = SocketState.LONG;
        } else if (status == SocketEvent.OPEN_READ){
            state = service(socketWrapper);
        } else {
            // Default to closing the socket if the SocketEvent passed in
            // is not consistent with the current state of the Processor
            state = SocketState.CLOSED;
        }

        if (getLog().isDebugEnabled()) {
            getLog().debug("Socket: [" + socketWrapper +
                           "], Status in: [" + status +
                           "], State out: [" + state + "]");
        }

        if (state != SocketState.CLOSED && isAsync()) {
            state = asyncPostProcess();
            if (getLog().isDebugEnabled()) {
                getLog().debug("Socket: [" + socketWrapper +
                               "], State after async post processing: [" + state + "]");
            }
        }

        if (dispatches == null || !dispatches.hasNext()) {
            // Only returns non-null iterator if there are
            // dispatches to process.
            dispatches = getIteratorAndClearDispatches();
        }
    } while (state == SocketState.ASYNC_END ||
             dispatches != null && state != SocketState.CLOSED);

    return state;
}
```

org.apache.coyote.http11.Http11Processor#service

```java
@Override
public SocketState service(SocketWrapperBase<?> socketWrapper)
    throws IOException {
    // 省略很多很多代码
    // 调用适配器的service
	getAdapter().service(request, response);
}
```

org.apache.catalina.connector.CoyoteAdapter#service

```java
@Override
public void service(org.apache.coyote.Request req, org.apache.coyote.Response res)
    throws Exception {
    // Calling the container
    // 省略代码
    // 适配器已经转好了协议
    // 从这里开始,就开始调用容器里的各个层级的责任链了
    // 这里调用的Engine的责任链
    connector.getService().getContainer().getPipeline().getFirst().invoke(request, response);
}
```

org.apache.catalina.core.StandardEngineValve#invoke

```java
@Override
public final void invoke(Request request, Response response)throws IOException, ServletException {

    // Select the Host to be used for this Request
    Host host = request.getHost();
    if (host == null) {
        response.sendError
            (HttpServletResponse.SC_BAD_REQUEST,
             sm.getString("standardEngine.noHost",
                          request.getServerName()));
        return;
    }
    if (request.isAsyncSupported()) {
        request.setAsyncSupported(host.getPipeline().isAsyncSupported());
    }

    // Ask this Host to process this request
    host.getPipeline().getFirst().invoke(request, response);

}
```

org.apache.catalina.core.StandardHostValve#invoke

```java
@Override
public final void invoke(Request request, Response response)
    throws IOException, ServletException {


    try {
        // 省略代码
        context.bind(Globals.IS_SECURITY_ENABLED, MY_CLASSLOADER);

        // Ask this Context to process this request. Requests that are
        // already in error must have been routed here to check for
        // application defined error pages so DO NOT forward them to the the
        // application for processing.
        try {
            if (!response.isErrorReportRequired()) {
                // 调用Context责任链
                context.getPipeline().getFirst().invoke(request, response);
            }
        } catch (Throwable t) {
          
        }

        
}
```

org.apache.catalina.core.StandardContextValve#invoke

```java
@Override
public final void invoke(Request request, Response response)
    // 省略代码
    wrapper.getPipeline().getFirst().invoke(request, response);
}
```

org.apache.catalina.core.StandardWrapperValve#invoke

```java
@Override
public final void invoke(Request request, Response response)throws IOException, ServletException {

    // Initialize local variables we may need
    boolean unavailable = false;
    Throwable throwable = null;
    // This should be a Request attribute...
    long t1=System.currentTimeMillis();
    requestCount.incrementAndGet();
    StandardWrapper wrapper = (StandardWrapper) getContainer();
    // 终于看到Servlet了
    Servlet servlet = null;
    Context context = (Context) wrapper.getParent();

    // Check for the application being marked unavailable
    if (!context.getState().isAvailable()) {
        response.sendError(HttpServletResponse.SC_SERVICE_UNAVAILABLE,
                           sm.getString("standardContext.isUnavailable"));
        unavailable = true;
    }

    // Check for the servlet being marked unavailable
    if (!unavailable && wrapper.isUnavailable()) {
        container.getLogger().info(sm.getString("standardWrapper.isUnavailable",
                                                wrapper.getName()));
        long available = wrapper.getAvailable();
        if ((available > 0L) && (available < Long.MAX_VALUE)) {
            response.setDateHeader("Retry-After", available);
            response.sendError(HttpServletResponse.SC_SERVICE_UNAVAILABLE,
                               sm.getString("standardWrapper.isUnavailable",
                                            wrapper.getName()));
        } else if (available == Long.MAX_VALUE) {
            response.sendError(HttpServletResponse.SC_NOT_FOUND,
                               sm.getString("standardWrapper.notFound",
                                            wrapper.getName()));
        }
        unavailable = true;
    }

    // Allocate a servlet instance to process this request
    try {
        if (!unavailable) {
            // 把当前请求的Servlet赋值
            servlet = wrapper.allocate();
        }
    } 
    // Create the filter chain for this request
    // 创建过滤器责任链
    ApplicationFilterChain filterChain =
        ApplicationFilterFactory.createFilterChain(request, wrapper, servlet);
    
    
	// 调用责任链
    filterChain.doFilter(request.getRequest(),response.getResponse());
}
```

org.apache.catalina.core.ApplicationFilterChain#doFilter

```java
@Override
public void doFilter(ServletRequest request, ServletResponse response)
    throws IOException, ServletException {

    if( Globals.IS_SECURITY_ENABLED ) {

    } else {
        // 省略代码
        internalDoFilter(request,response);
    }
}
```

org.apache.catalina.core.ApplicationFilterChain#internalDoFilter

```java
private void internalDoFilter(ServletRequest request,
                                  ServletResponse response)
        throws IOException, ServletException {

        // Call the next filter if there is one
    	// 开始循环调用过滤器
        if (pos < n) {
            ApplicationFilterConfig filterConfig = filters[pos++];
            try {
                Filter filter = filterConfig.getFilter();

                if (request.isAsyncSupported() && "false".equalsIgnoreCase(
                        filterConfig.getFilterDef().getAsyncSupported())) {
                    request.setAttribute(Globals.ASYNC_SUPPORTED_ATTR, Boolean.FALSE);
                }
                if( Globals.IS_SECURITY_ENABLED ) {
                    final ServletRequest req = request;
                    final ServletResponse res = response;
                    Principal principal =
                        ((HttpServletRequest) req).getUserPrincipal();

                    Object[] args = new Object[]{req, res, this};
                    SecurityUtil.doAsPrivilege ("doFilter", filter, classType, args, principal);
                } else {
                    // 执行过滤器
                    filter.doFilter(request, response, this);
                }
            } catch (IOException | ServletException | RuntimeException e) {
                throw e;
            } catch (Throwable e) {
                e = ExceptionUtils.unwrapInvocationTargetException(e);
                ExceptionUtils.handleThrowable(e);
                throw new ServletException(sm.getString("filterChain.filter"), e);
            }
            return;
        }

        // We fell off the end of the chain -- call the servlet instance
        try {
            if (ApplicationDispatcher.WRAP_SAME_OBJECT) {
                lastServicedRequest.set(request);
                lastServicedResponse.set(response);
            }

            if (request.isAsyncSupported() && !servletSupportsAsync) {
                request.setAttribute(Globals.ASYNC_SUPPORTED_ATTR,
                        Boolean.FALSE);
            }
            // Use potentially wrapped request from this point
            if ((request instanceof HttpServletRequest) &&
                    (response instanceof HttpServletResponse) &&
                    Globals.IS_SECURITY_ENABLED ) {
                final ServletRequest req = request;
                final ServletResponse res = response;
                Principal principal =
                    ((HttpServletRequest) req).getUserPrincipal();
                Object[] args = new Object[]{req, res};
                SecurityUtil.doAsPrivilege("service",
                                           servlet,
                                           classTypeUsedInService,
                                           args,
                                           principal);
            } else {
                // 执行完过滤器,就开始要调用HttpServlet的方法
                servlet.service(request, response);
            }
        } catch (IOException | ServletException | RuntimeException e) {
            throw e;
        } catch (Throwable e) {
            e = ExceptionUtils.unwrapInvocationTargetException(e);
            ExceptionUtils.handleThrowable(e);
            throw new ServletException(sm.getString("filterChain.servlet"), e);
        } finally {
            if (ApplicationDispatcher.WRAP_SAME_OBJECT) {
                lastServicedRequest.set(null);
                lastServicedResponse.set(null);
            }
        }
    }
```

javax.servlet.http.HttpServlet#service(javax.servlet.ServletRequest, javax.servlet.ServletResponse)

```java
@Override
public void service(ServletRequest req, ServletResponse res)
    throws ServletException, IOException {

    HttpServletRequest  request;
    HttpServletResponse response;

    try {
        request = (HttpServletRequest) req;
        response = (HttpServletResponse) res;
    } catch (ClassCastException e) {
        throw new ServletException("non-HTTP request or response");
    }
    service(request, response);
}
```

```java
protected void service(HttpServletRequest req, HttpServletResponse resp)
    throws ServletException, IOException {

    String method = req.getMethod();

    if (method.equals(METHOD_GET)) {
        long lastModified = getLastModified(req);
        if (lastModified == -1) {
            // servlet doesn't support if-modified-since, no reason
            // to go through further expensive logic
            // 这里就开始调用我们自己写的Servlet的方法了
            doGet(req, resp);
        } else {


    } else if (method.equals(METHOD_HEAD)) {
        long lastModified = getLastModified(req);
        maybeSetLastModified(resp, lastModified);
        doHead(req, resp);

    } else if (method.equals(METHOD_POST)) {
        doPost(req, resp);

    } else if (method.equals(METHOD_PUT)) {
        doPut(req, resp);

    } else if (method.equals(METHOD_DELETE)) {
        doDelete(req, resp);

    } else if (method.equals(METHOD_OPTIONS)) {
        doOptions(req,resp);

    } else if (method.equals(METHOD_TRACE)) {
        doTrace(req,resp);

    } else {
        //
        // Note that this means NO servlet supports whatever
        // method was requested, anywhere on this server.
        //

        String errMsg = lStrings.getString("http.method_not_implemented");
        Object[] errArgs = new Object[1];
        errArgs[0] = method;
        errMsg = MessageFormat.format(errMsg, errArgs);

            resp.sendError(HttpServletResponse.SC_NOT_IMPLEMENTED, errMsg);
        }
    }
```

自己写的Setvlet

```java
public class BBSServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("====================get===========");
    }


    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("====================post===========");
    }
}
```

