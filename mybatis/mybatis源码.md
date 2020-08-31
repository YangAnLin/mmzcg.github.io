---
typora-root-url: ..\img
---

[toc]
## 1.最开始的
### 1.启动类
```java
String resource = "com/analyze/mybatis/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
SqlSession sqlSession = sqlSessionFactory.openSession();
Map map = sqlSession.selectOne("com.analyze.mybatis.mapper.UserMapper.getUA");
sqlSession.close();

String resource = "com/analyze/mybatis/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
SqlSession sqlSession = sqlSessionFactory.openSession();
UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
Map map = userMapper.getUA();
sqlSession.close();
```

2.第2行代码读取Resource,本来可以直接用

```java
InputStream resourceAsStream = this.getClass().getClassLoader().getResourceAsStream(resource);
```
但是在源码里被封装起来了,下面的代码都是公共类
```java
public static Reader getResourceAsReader(String resource) throws IOException {
    Reader reader;
    if (charset == null) {
        reader = new InputStreamReader(getResourceAsStream(resource));
    } else {
        reader = new InputStreamReader(getResourceAsStream(resource), charset);
    }
    return reader;
}
```

```java
public static InputStream getResourceAsStream(String resource) throws IOException {
    // 通过Classloader加载资源文件
    return getResourceAsStream(null, resource);
}
```

ClassLoaderWrapper 类的初始化
```java
public class ClassLoaderWrapper {

  ClassLoader defaultClassLoader;
  ClassLoader systemClassLoader;

  ClassLoaderWrapper() {
    try {
      systemClassLoader = ClassLoader.getSystemClassLoader();
    } catch (SecurityException ignored) {
      // AccessControlException on Google App Engine   
    }
  }
}
```
```java
private static ClassLoaderWrapper classLoaderWrapper = new ClassLoaderWrapper();

public static InputStream getResourceAsStream(ClassLoader loader, String resource) throws IOException {
    // 通过ClassLoader来加载资源,此时的resource为null
    InputStream in = classLoaderWrapper.getResourceAsStream(resource, loader);
    if (in == null) {
        throw new IOException("Could not find resource " + resource);
    }
    return in;
}
```

```java
public InputStream getResourceAsStream(String resource, ClassLoader classLoader) {
    return getResourceAsStream(resource, getClassLoaders(classLoader));
}
```

```java
ClassLoader[] getClassLoaders(ClassLoader classLoader) {
    return new ClassLoader[]{
        classLoader,
        defaultClassLoader,
        Thread.currentThread().getContextClassLoader(),
        getClass().getClassLoader(),
        systemClassLoader};
}
```

找到一个可以用的Classloader
```java
InputStream getResourceAsStream(String resource, ClassLoader[] classLoader) {
    for (ClassLoader cl : classLoader) {
        if (null != cl) {

            // try to find the resource as passed
            InputStream returnValue = cl.getResourceAsStream(resource);

            // now, some class loaders want this leading "/", so we'll add it and try again if we didn't find the resource
            if (null == returnValue) {
                returnValue = cl.getResourceAsStream("/" + resource);
            }

            if (null != returnValue) {
                return returnValue;
            }
        }
    }
    return null;
}
```

### 2.现在回到第3行代码

```java
public class SqlSessionFactoryBuilder {

  // 1.参数是文件的输入流,这里用到了建造者模式
  public SqlSessionFactory build(InputStream inputStream) {
    return build(inputStream, null, null);
  }

  // 2.读取xml配置文件,生成XMLConfigBuilder对象
  public SqlSessionFactory build(InputStream inputStream, String environment, Properties properties) {
    try {
      XMLConfigBuilder parser = new XMLConfigBuilder(inputStream, environment, properties);
      return build(parser.parse());
    } catch (Exception e) {
      throw ExceptionFactory.wrapException("Error building SqlSession.", e);
    } finally {
      ErrorContext.instance().reset();
      try {
        inputStream.close();
      } catch (IOException e) {
        // Intentionally ignore. Prefer previous error.
      }
    }
  }

  // 3.重载方法,生成DefaultSqlSessionFactory
  public SqlSessionFactory build(Configuration config) {
    return new DefaultSqlSessionFactory(config);
  }

}
```

```java
public XMLConfigBuilder(Reader reader, String environment, Properties props) {
    this(new XPathParser(reader, true, props, new XMLMapperEntityResolver()), environment, props);
}
```

```java
public XPathParser(Reader reader, boolean validation, Properties variables, EntityResolver entityResolver) {
    commonConstructor(validation, variables, entityResolver);
    this.document = createDocument(new InputSource(reader));
}
```

```java
private void commonConstructor(boolean validation, Properties variables, EntityResolver entityResolver) {
    this.validation = validation;
    this.entityResolver = entityResolver;
    this.variables = variables;
    XPathFactory factory = XPathFactory.newInstance();
    this.xpath = factory.newXPath();
}
```

```java
private XMLConfigBuilder(XPathParser parser, String environment, Properties props) {
    //  创建Configuration对象，并通过TypeAliasRegistry注册一些Mybatis内部相关类的别名
    super(new Configuration());
    ErrorContext.instance().resource("SQL Mapper Configuration");
    this.configuration.setVariables(props);
    this.parsed = false;
    this.environment = environment;
    this.parser = parser;
}
```

回到build方法`build(parser.parse());

```java
/**
   * 解析XML配置文件
   * @return
   */
public Configuration parse() {
    if (parsed) {
        throw new BuilderException("Each XMLConfigBuilder can only be used once.");
    }
    parsed = true;
    // parser.evalNode("/configuration")：通过XPATH解析器，解析configuration根节点
    // 从configuration根节点开始解析，最终将解析出的内容封装到Configuration对象中
    parseConfiguration(parser.evalNode("/configuration"));
    return configuration;
}
```

```java
private void parseConfiguration(XNode root) {
    try {
      //issue #117 read properties first
      // 解析</properties>标签
      propertiesElement(root.evalNode("properties"));
      // 解析</settings>标签
      Properties settings = settingsAsProperties(root.evalNode("settings"));
      loadCustomVfs(settings);
      loadCustomLogImpl(settings);
      // 解析</typeAliases>标签
      typeAliasesElement(root.evalNode("typeAliases"));
      // 解析</plugins>标签
      pluginElement(root.evalNode("plugins"));
      // 解析</objectFactory>标签
      objectFactoryElement(root.evalNode("objectFactory"));
      // 解析</objectWrapperFactory>标签
      objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
      // 解析</reflectorFactory>标签
      reflectorFactoryElement(root.evalNode("reflectorFactory"));
      settingsElement(settings);
      
      // read it after objectFactory and objectWrapperFactory issue #631
      // 解析</environments>标签
      environmentsElement(root.evalNode("environments"));
      // 解析</databaseIdProvider>标签
      databaseIdProviderElement(root.evalNode("databaseIdProvider"));
      // 解析</typeHandlers>标签
      typeHandlerElement(root.evalNode("typeHandlers"));
      // 解析</mappers>标签
      mapperElement(root.evalNode("mappers"));
    } catch (Exception e) {
      throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + e, e);
    }
  }
```

```java
/**
   * 解析<mappers>标签
   * @param parent  mappers标签对应的XNode对象
   * @throws Exception
   */
  private void mapperElement(XNode parent) throws Exception {
    if (parent != null) {
      // 获取<mappers>标签的子标签
      for (XNode child : parent.getChildren()) {
    	// <package>子标签
        if ("package".equals(child.getName())) {
          // 获取mapper接口和mapper映射文件对应的package包名
          String mapperPackage = child.getStringAttribute("name");
          // 将包下所有的mapper接口以及它的代理对象存储到一个Map集合中，key为mapper接口类型，value为代理对象工厂
          configuration.addMappers(mapperPackage);
        } else {// <mapper>子标签
          // 获取<mapper>子标签的resource属性
          String resource = child.getStringAttribute("resource");
          // 获取<mapper>子标签的url属性
          String url = child.getStringAttribute("url");
          // 获取<mapper>子标签的class属性
          String mapperClass = child.getStringAttribute("class");
          // 它们是互斥的
          if (resource != null && url == null && mapperClass == null) {
            ErrorContext.instance().resource(resource);
            InputStream inputStream = Resources.getResourceAsStream(resource);
            // 专门用来解析mapper映射文件
            XMLMapperBuilder mapperParser = new XMLMapperBuilder(inputStream, configuration, resource, configuration.getSqlFragments());
            // 通过XMLMapperBuilder解析mapper映射文件
            mapperParser.parse();
          } else if (resource == null && url != null && mapperClass == null) {
            ErrorContext.instance().resource(url);
            InputStream inputStream = Resources.getUrlAsStream(url);
            XMLMapperBuilder mapperParser = new XMLMapperBuilder(inputStream, configuration, url, configuration.getSqlFragments());
            // 通过XMLMapperBuilder解析mapper映射文件
            mapperParser.parse();
          } else if (resource == null && url == null && mapperClass != null) {
            Class<?> mapperInterface = Resources.classForName(mapperClass);
            // 将指定mapper接口以及它的代理对象存储到一个Map集合中，key为mapper接口类型，value为代理对象工厂
            configuration.addMapper(mapperInterface);
          } else {
            throw new BuilderException("A mapper element may only specify a url, resource or class, but not more than one.");
          }
        }
      }
    }
  }
```

第36行代码

```java
  public void parse() {
	// mapper映射文件是否已经加载过
    if (!configuration.isResourceLoaded(resource)) {
      // 从映射文件中的<mapper>根标签开始解析，直到完整的解析完毕
      configurationElement(parser.evalNode("/mapper"));
      // 标记已经解析
      configuration.addLoadedResource(resource);
      bindMapperForNamespace();
    }

    parsePendingResultMaps();
    parsePendingCacheRefs();
    parsePendingStatements();
  }
```

```java
private void configurationElement(XNode context) {
    try {
      // 获取<mapper>标签的namespace值，也就是命名空间
      String namespace = context.getStringAttribute("namespace");
      // 命名空间不能为空
      if (namespace == null || namespace.equals("")) {
        throw new BuilderException("Mapper's namespace cannot be empty");
      }
      
      // 设置当前的命名空间为namespace的值
      builderAssistant.setCurrentNamespace(namespace);
      // 解析<cache-ref>子标签
      cacheRefElement(context.evalNode("cache-ref"));
      // 解析<cache>子标签
      cacheElement(context.evalNode("cache"));
      
      // 解析<parameterMap>子标签
      parameterMapElement(context.evalNodes("/mapper/parameterMap"));
      // 解析<resultMap>子标签
      resultMapElements(context.evalNodes("/mapper/resultMap"));
      // 解析<sql>子标签，也就是SQL片段
      sqlElement(context.evalNodes("/mapper/sql"));
      // 解析<select>\<insert>\<update>\<delete>子标签
      buildStatementFromContext(context.evalNodes("select|insert|update|delete"));
    } catch (Exception e) {
      throw new BuilderException("Error parsing Mapper XML. The XML location is '" + resource + "'. Cause: " + e, e);
    }
  }
```

看24行

```java
  private void buildStatementFromContext(List<XNode> list) {
    if (configuration.getDatabaseId() != null) {
      buildStatementFromContext(list, configuration.getDatabaseId());
    }
    // 构建MappedStatement
    buildStatementFromContext(list, null);
  }

  private void buildStatementFromContext(List<XNode> list, String requiredDatabaseId) {
    for (XNode context : list) {
      // MappedStatement解析器
      final XMLStatementBuilder statementParser = new XMLStatementBuilder(configuration, builderAssistant, context, requiredDatabaseId);
      try {
    	// 解析select等4个标签，创建MappedStatement对象
        statementParser.parseStatementNode();
      } catch (IncompleteElementException e) {
        configuration.addIncompleteStatement(statementParser);
      }
    }
  }
```

看15行,到下面这里,`sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);`这里的代码就已经看完了

```java
public void parseStatementNode() {
	// 获取statement的id属性（特别关键的值）
    String id = context.getStringAttribute("id");
    String databaseId = context.getStringAttribute("databaseId");

    if (!databaseIdMatchesCurrent(id, databaseId, this.requiredDatabaseId)) {
      return;
    }

    Integer fetchSize = context.getIntAttribute("fetchSize");
    Integer timeout = context.getIntAttribute("timeout");
    String parameterMap = context.getStringAttribute("parameterMap");
    // 获取入参类型
    String parameterType = context.getStringAttribute("parameterType");
    // 别名处理，获取入参对应的Java类型
    Class<?> parameterTypeClass = resolveClass(parameterType);
    // 获取ResultMap
    String resultMap = context.getStringAttribute("resultMap");
    // 获取结果映射类型
    String resultType = context.getStringAttribute("resultType");
    String lang = context.getStringAttribute("lang");
    LanguageDriver langDriver = getLanguageDriver(lang);
    
    // 别名处理，获取返回值对应的Java类型
    Class<?> resultTypeClass = resolveClass(resultType);
    String resultSetType = context.getStringAttribute("resultSetType");
    
    // 设置默认StatementType为Prepared，该参数指定了后面的JDBC处理时，采用哪种Statement
    StatementType statementType = StatementType.valueOf(context.getStringAttribute("statementType", StatementType.PREPARED.toString()));
    ResultSetType resultSetTypeEnum = resolveResultSetType(resultSetType);

    String nodeName = context.getNode().getNodeName();
    // 解析SQL命令类型是什么？确定操作是CRUD中的哪一种
    SqlCommandType sqlCommandType = SqlCommandType.valueOf(nodeName.toUpperCase(Locale.ENGLISH));
    //是否查询语句
    boolean isSelect = sqlCommandType == SqlCommandType.SELECT;
    boolean flushCache = context.getBooleanAttribute("flushCache", !isSelect);
    boolean useCache = context.getBooleanAttribute("useCache", isSelect);
    boolean resultOrdered = context.getBooleanAttribute("resultOrdered", false);

    // Include Fragments before parsing
    // <include>标签解析
    XMLIncludeTransformer includeParser = new XMLIncludeTransformer(configuration, builderAssistant);
    includeParser.applyIncludes(context.getNode());

    // Parse selectKey after includes and remove them.
    // 解析<selectKey>标签
    processSelectKeyNodes(id, parameterTypeClass, langDriver);
    
    // Parse the SQL (pre: <selectKey> and <include> were parsed and removed)
    // 创建SqlSource，解析SQL，封装SQL语句（未参数绑定）和入参信息
    SqlSource sqlSource = langDriver.createSqlSource(configuration, context, parameterTypeClass);
   
    String resultSets = context.getStringAttribute("resultSets");
    String keyProperty = context.getStringAttribute("keyProperty");
    String keyColumn = context.getStringAttribute("keyColumn");
    KeyGenerator keyGenerator;
    String keyStatementId = id + SelectKeyGenerator.SELECT_KEY_SUFFIX;
    keyStatementId = builderAssistant.applyCurrentNamespace(keyStatementId, true);
    if (configuration.hasKeyGenerator(keyStatementId)) {
      keyGenerator = configuration.getKeyGenerator(keyStatementId);
    } else {
      keyGenerator = context.getBooleanAttribute("useGeneratedKeys",
          configuration.isUseGeneratedKeys() && SqlCommandType.INSERT.equals(sqlCommandType))
          ? Jdbc3KeyGenerator.INSTANCE : NoKeyGenerator.INSTANCE;
    }

    // 通过构建者助手，创建MappedStatement对象
    builderAssistant.addMappedStatement(id, sqlSource, statementType, sqlCommandType,
        fetchSize, timeout, parameterMap, parameterTypeClass, resultMap, resultTypeClass,
        resultSetTypeEnum, flushCache, useCache, resultOrdered, 
        keyGenerator, keyProperty, keyColumn, databaseId, langDriver, resultSets);
  }
```

看`session = sqlSessionFactory.openSession();`

```java
  @Override
  public SqlSession openSession() {
    return openSessionFromDataSource(configuration.getDefaultExecutorType(), null, false);
  }
```

```java
private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level, boolean autoCommit) {
    Transaction tx = null;
    try {
      // 获取数据源环境信息
      final Environment environment = configuration.getEnvironment();
      // 获取事务工厂
      final TransactionFactory transactionFactory = getTransactionFactoryFromEnvironment(environment);
      // 获取JdbcTransaction或者ManagedTransaction
      tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);
      // 创建Executor执行器
      final Executor executor = configuration.newExecutor(tx, execType);
      // 创建DefaultSqlSession
      return new DefaultSqlSession(configuration, executor, autoCommit);
    } catch (Exception e) {
      closeTransaction(tx); // may have fetched a connection so lets call close()
      throw ExceptionFactory.wrapException("Error opening session.  Cause: " + e, e);
    } finally {
      ErrorContext.instance().reset();
    }
  }
```

看13行

```java

```

















### 3.事务管理器

```xml
<?xml version="1.0" encoding="UTF-8" ?><!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
      <environments default="development">
        <!-- 运行环境 -->
        <environment id="development">
          <!-- 事务管理器类型 -->
          <transactionManager type="JDBC"/>
          <dataSource type="POOLED">
            <property name="driver" value="${driver}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${password}"/>
          </dataSource>
        </environment>
      </environments>
      <mappers>
        <mapper resource="org/mybatis/example/BlogMapper.xml"/>
      </mappers>
  </configuration>
```

读取完配置了,返回`DefaultSqlSessionFactory -> SqlSessionFactory `

```java
SqlSession session = sqlSessionFactory.openSession();
```

DefaultSqlSessionFactory里的openSession
```java
public SqlSession openSession() {
  return openSessionFromDataSource(configuration.getDefaultExecutorType(), null, false);
}


private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level, boolean autoCommit) {
    Transaction tx = null;
    try {
      //根据配置获取环境
      final Environment environment = configuration.getEnvironment();
      //构建事务工厂(返回的是:JdbcTransactionFactory,配置文件配的是:JDBC)
      //environment 就是xml里配置的账号密码
      final TransactionFactory transactionFactory = getTransactionFactoryFromEnvironment(environment);
      //通过事务工厂创建事务
      tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);
      final Executor executor = configuration.newExecutor(tx, execType);
      return new DefaultSqlSession(configuration, executor, autoCommit);
    } catch (Exception e) {
      closeTransaction(tx); // may have fetched a connection so lets call close()
      throw ExceptionFactory.wrapException("Error opening session.  Cause: " + e, e);
    } finally {
      ErrorContext.instance().reset();
    }
  }
```

* `configuration.getDefaultExecutorType()`的值就是jdbc

### 4.SqlSession
从上面方法的`final Executor executor = configuration.newExecutor(tx, execType);`


```java

class Configuration{

    //Configuration的构造函数,有下面这些默认的设置
    //默认执行器类型
    protected ExecutorType defaultExecutorType = ExecutorType.SIMPLE;
    //二级缓存的全局开关,默认开启缓存
    protected boolean cacheEnabled = true;

    public Executor newExecutor(Transaction transaction, ExecutorType executorType) {
        //executorType为null时executorType=ExecutorType.SIMPLE
        executorType = executorType == null ? this.defaultExecutorType : executorType;
        //根据执行器类型创建执行器
        executorType = executorType == null ? ExecutorType.SIMPLE : executorType;
        Object executor;
        if (ExecutorType.BATCH == executorType) {
            executor = new BatchExecutor(this, transaction);
        } else if (ExecutorType.REUSE == executorType) {
            executor = new ReuseExecutor(this, transaction);
        } else {
            executor = new SimpleExecutor(this, transaction);
        }
        //当cacheEnabled为true时创建CachingExecutor对象
        if (this.cacheEnabled) {
            executor = new CachingExecutor((Executor)executor);
        }

        Executor executor = (Executor)this.interceptorChain.pluginAll(executor);
        return executor;
    }
}
```

配置二级缓存
```xml
<settings>
  <setting name="cacheEnabled" value="true"/>
</settings>
```

openSessionFromDataSource 方法中,创建了`Executor`对象,就接着创建`DefaultSqlSession`对象


### 5.mapper 动态代理
用`启动类`中的方法,对比可以发现两段代码不同之处为:
```java
Map map = sqlSession.selectOne("com.analyze.mybatis.mapper.UserMapper.getUA");

UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
Map map = userMapper.getUA();
```

#### 1.第一种查询方法
在查看`DefaultSqlSession`中的`selectOne`方法,会执行以下的调用链
```java
public <T> T selectOne(String statement) {
        return this.selectOne(statement, (Object)null);
    }
    
public <T> T selectOne(String statement, Object parameter) {
    List<T> list = this.selectList(statement, parameter);
    if (list.size() == 1) {
        return list.get(0);
    } else if (list.size() > 1) {
        throw new TooManyResultsException("Expected one result (or null) to be returned by selectOne(), but found: " + list.size());
    } else {
        return null;
    }
}

public <E> List<E> selectList(String statement, Object parameter) {
    return this.selectList(statement, parameter, RowBounds.DEFAULT);
}

public <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds) {
    List var5;
    try {
        //从配置中获取statement信息
        MappedStatement ms = this.configuration.getMappedStatement(statement);
        //调用执行器
        var5 = this.executor.query(ms, this.wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);
    } catch (Exception var9) {
        throw ExceptionFactory.wrapException("Error querying database.  Cause: " + var9, var9);
    } finally {
        ErrorContext.instance().reset();
    }

    return var5;
}
```

#### 2.第二种查询方法
DefaultSqlSession.java
```java
public <T> T getMapper(Class<T> type) {
    return this.configuration.getMapper(type, this);
}

public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
    return this.mapperRegistry.getMapper(type, sqlSession);
}

public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
    MapperProxyFactory<T> mapperProxyFactory = (MapperProxyFactory)this.knownMappers.get(type);
    if (mapperProxyFactory == null) {
        throw new BindingException("Type " + type + " is not known to the MapperRegistry.");
    } else {
        try {
            return mapperProxyFactory.newInstance(sqlSession);
        } catch (Exception var5) {
            throw new BindingException("Error getting mapper instance. Cause: " + var5, var5);
        }
    }
}

public T newInstance(SqlSession sqlSession) {
    //从newInstance里的MapperProxy很容易就可以看出使用了动态代理.
    MapperProxy<T> mapperProxy = new MapperProxy(sqlSession, this.mapperInterface, this.methodCache);
    return this.newInstance(mapperProxy);
}

public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    try {
        if (Object.class.equals(method.getDeclaringClass())) {
            return method.invoke(this, args);
        }

        if (this.isDefaultMethod(method)) {
            return this.invokeDefaultMethod(proxy, method, args);
        }
    } catch (Throwable var5) {
        throw ExceptionUtil.unwrapThrowable(var5);
    }

    MapperMethod mapperMethod = this.cachedMapperMethod(method);
    return mapperMethod.execute(this.sqlSession, args);
}

private MapperMethod cachedMapperMethod(Method method) {
    MapperMethod mapperMethod = (MapperMethod)this.methodCache.get(method);
    if (mapperMethod == null) {
        mapperMethod = new MapperMethod(this.mapperInterface, method, this.sqlSession.getConfiguration());
        this.methodCache.put(method, mapperMethod);
    }

    return mapperMethod;
}

```

MapperMethod.java
```java
private final MapperMethod.SqlCommand command;
private final MapperMethod.MethodSignature method;

public MapperMethod(Class<?> mapperInterface, Method method, Configuration config) {
    this.command = new MapperMethod.SqlCommand(config, mapperInterface, method);
    this.method = new MapperMethod.MethodSignature(config, mapperInterface, method);
}

public Object execute(SqlSession sqlSession, Object[] args) {
    Object param;
    Object result;
    switch(this.command.getType()) {
    case INSERT:
        param = this.method.convertArgsToSqlCommandParam(args);
        result = this.rowCountResult(sqlSession.insert(this.command.getName(), param));
        break;
    case UPDATE:
        param = this.method.convertArgsToSqlCommandParam(args);
        result = this.rowCountResult(sqlSession.update(this.command.getName(), param));
        break;
    case DELETE:
        param = this.method.convertArgsToSqlCommandParam(args);
        result = this.rowCountResult(sqlSession.delete(this.command.getName(), param));
        break;
    case SELECT:
        if (this.method.returnsVoid() && this.method.hasResultHandler()) {
            this.executeWithResultHandler(sqlSession, args);
            result = null;
        } else if (this.method.returnsMany()) {
            result = this.executeForMany(sqlSession, args);
        } else if (this.method.returnsMap()) {
            result = this.executeForMap(sqlSession, args);
        } else if (this.method.returnsCursor()) {
            result = this.executeForCursor(sqlSession, args);
        } else {
            param = this.method.convertArgsToSqlCommandParam(args);
            result = sqlSession.selectOne(this.command.getName(), param);
        }
        break;
    case FLUSH:
        result = sqlSession.flushStatements();
        break;
    default:
        throw new BindingException("Unknown execution method for: " + this.command.getName());
    }

    if (result == null && this.method.getReturnType().isPrimitive() && !this.method.returnsVoid()) {
        throw new BindingException("Mapper method '" + this.command.getName() + " attempted to return null from a method with a primitive return type (" + this.method.getReturnType() + ").");
    } else {
        return result;
    }
}
```

# 面试题

## 流程

1. 定义接口 Mapper 和方法，用来调用数据库操作。Mapper 接口操作数据库需要通过代理类。
2. 定义配置类对象 Configuration。
3. 定义应用层的 API SqlSession。它有一个 getMapper()方法，我们会从配置类Configuration 里面使用 Proxy.newProxyInatance()拿到一个代理对象MapperProxy。
4. 有了代理对象 MapperProxy 之后，我们调用接口的任意方法，就是调用代理对象的 invoke()方法。
5. 代理对象 MapperProxy 的 invoke()方法调用了 SqlSession 的 selectOne()。
6. SqlSession 只是一个 API，还不是真正的 SQL 执行者，所以接下来会调用执行器 Executor 的 query()方法。

## PrepareStatement 和 Statement 的区别

```
两个都是接口,PrepareStatement 是继承自 Statement 的；
Statement 处理静态 SQL，PreparedStatement 主要用于执行带参数的语句；
PreparedStatement 的 addBatch()方法一次性发送多个查询给数据库；
PS 相似 SQL 只编译一次（对语句进行了缓存，相当于一个函数），减少编译次数；
PS 可以防止 SQL 注入；
MyBatis 默认值：PREPARED 也就是PreparedStatement
```

## MyBatis 编程式开发中的核心对象及其作用？

```
SqlSessionFactoryBuilder 创建工厂类
SqlSessionFactory 创建会话
SqlSession 提供操作接口
MapperProxy 代理 Mapper 接口后，用于找到 SQL 执行
```

## Java 类型和数据库类型怎么实现相互映射？

通过 TypeHandler，例如 Java 类型中的 String 要保存成 varchar，就会自动调用相应的 Handler。如果没有系统自带的 TypeHandler，也可以自定义--比如之前文章中写的json转对象的操作。

## MyBatis 翻页的几种方式和区别？

逻辑翻页：通过 RowBounds 对象。
物理翻页：通过改写 SQL 语句，可用插件拦截 Executor 实现。

## 解析全局配置文件的时候，做了什么？

```
创建 Configuration，设置 Configuration
解析 Mapper.xml，设置 MappedStatement
```

## 没有实现类，MyBatis 的方法是怎么执行的？

MapperProxy 代理，代理类的 invoke()方法中调用了 SqlSession.selectOne()

## 接口方法和映射器的 statement id 是怎么绑定起来的？

MappedStatement 对象中存储了 statement 和 SQL 的映射关系

## MyBatis 插件怎么编写和使用？原理是什么?

```
使用：继承 Interceptor 接口，加上注解，在 mybatis-config.xml 中配置
原理：动态代理，责任链模式，使用 Plugin 创建代理对象
在被拦截对象的方法调用的时候，先走到 Plugin 的 invoke()方法，再走到Interceptor 实现类的 intercept()方法，
最后通过 Invocation.proceed()方法调用被拦截对象的原方法
```

## DefaulSqlSession 和 SqlSessionTemplate 的区别是什么？

```
一个线程安全一个线程不安全
1）为什么 SqlSessionTemplate 是线程安全的？
其内部类 SqlSessionInterceptor 的 invoke()方法中的 getSqlSession()方法：如果当前线程已经有存在的 
SqlSession 对象，会在 ThreadLocal 的容器中拿到SqlSessionHolder，获取 DefaultSqlSession。
如果没有，则会 new 一个 SqlSession，并且绑定到 SqlSessionHolder，放到ThreadLocal 中。SqlSessionTemplate 
中在同一个事务中使用同一个 SqlSession。调用 closeSqlSession()关闭会话时，如果存在事务，减少 holder 的引用计数。否则直接关闭 SqlSession。
2) 在编程式的开发中，有什么方法保证 SqlSession 的线程安全？
SqlSessionManager 同时实现了 SqlSessionFactory、SqlSession 接口，通过ThreadLocal 容器维护 SqlSession。
```