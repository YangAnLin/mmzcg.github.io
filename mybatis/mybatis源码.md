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

### 2.SqlSessionFactory
```java
public class SqlSessionFactoryBuilder {

  // 1.参数是文件的输入流
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

## 2.开课吧