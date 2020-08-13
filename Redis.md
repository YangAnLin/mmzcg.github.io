
## Redis环境变量
### 一.开机启动
启动脚本 `redis_init_script` 位于位于`Redis`的 `/utils/` 目录下。

大致浏览下该启动脚本，发现redis习惯性用监听的端口名作为配置文件等命名，我们后面也遵循这个约定。
```
#redis服务器监听的端口
REDISPORT=6379
#服务端所处位置，在make install后默认存放与`/usr/local/bin/redis-server`，如果未make install则需要修改该路径，下同。
EXEC=/usr/local/bin/redis-server
#客户端位置
CLIEXEC=/usr/local/bin/redis-cli
#Redis的PID文件位置
PIDFILE=/var/run/redis_${REDISPORT}.pid
#配置文件位置，需要修改
CONF="/etc/redis/${REDISPORT}.conf"
配置环境
```

* 1.根据启动脚本要求，将修改好的配置文件以端口为名复制一份到指定目录。需使用root用户。

```
mkdir /etc/redis
cp redis.conf /etc/redis/6379.conf
```
* 2.将启动脚本复制到`/etc/init.d`目录下，本例将启动脚本命名为redisd（通常都以d结尾表示是后台自启动服务）。

```
cp redis_init_script /etc/init.d/redisd
```

* 3.设置为开机自启动

此处直接配置开启自启动 `chkconfig redisd on` 将报错误： `service redisd does not support chkconfig` 
参照 此篇文章 ，在启动脚本开头添加如下两行注释以修改其运行级别：

```
#!/bin/sh
# chkconfig:   2345 90 10
# description:  Redis is a persistent key-value database
#
```
 再设置即可成功。

```
#设置为开机自启动服务器
chkconfig redisd on
#打开服务
service redisd start
#关闭服务
service redisd stop
```

### 二.添加到PATH
#### 问题描述:  

由于redis-cli命令没有设置到PATH中,每次想使用时,都需要执行find命令去找这个命令在哪里
```
# find / -name redis-cli  
```
找到之后, 再执行命令, 这样实在太麻烦

#### 解决方案:  

将`redis-cli`命令配置到`PATH`中,这样每次使用时,就像`ls`这种命令一样不加路径执行

```
# vi ~/.bash_profile  
```
将`redis-cli`命令路径配置到`PATH`中

```
PATH=$PATH:$HOME/bin:/usr/local/redis-3.2.8/src/  
```
保存之后, 使用source命令使之生效

```
# source ~/.bash_profile  
```



## 



* 

# 2.为什么要用 Redis/为什么要用缓存

**高性能：**

假如用户第一次访问数据库中的某些数据。这个过程会比较慢，因为是从硬盘上读取的。将该用户访问的数据存在数缓存中，这样下一次再访问这些数据的时候就可以直接从缓存中获取了。操作缓存就是直接操作内存，所以速度相当快。如果数据库中的对应数据改变的之后，同步改变缓存中相应的数据即可！

![](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-9-24/54316596.jpg)


**高并发：**

直接操作缓存能够承受的请求是远远大于直接访问数据库的，所以我们可以考虑把数据库中的部分数据转移到缓存中去，这样用户的一部分请求会直接到缓存这里而不用经过数据库。

![](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-9-24/85146760.jpg)

# 

# 4.redis 和 memcached 的区别

| 对比参数     | Redis                                                        | Memcached                                                  |
| ------------ | ------------------------------------------------------------ | ---------------------------------------------------------- |
| 类型         | 1.支持内存<br/>  2.非关系型数据库                            | 1.支持内存<br/>2.key-value键值对<br/>3.缓存系统            |
| 数据存储类型 | 1.String<br/>2.List<br/>3.Set<br/>4.Hash<br/>5.SortSet(ZSet) | 1.文本型<br/>2.二进制类型                                  |
| 查询操作     | 1.批量操作<br/>2.事务支持(假的事务)<br/>3.每个类型的CRUD     | CURD                                                       |
| 附加功能     | 1.发布/订阅模式<br/>2.主从分区<br/>3.序列化支持<br/>4.Lua脚本支持 | 多线程服务支持                                             |
| 网络IO模型   | 单线程的多路IO复用                                           | 多线程非阻塞IO复用                                         |
| 事件库       | 自封装简易事件库AeEvent                                      | 贵族血统的LibEvent事件库                                   |
| 持久化支持   | 1.RDB(快照)<br/>2.AOF(持久化)                                | 不支持                                                     |
| 集群模式     | 1.主从<br/>2分片集群                                         | 有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据 |


# 5.redis 常见数据结构以及使用场景分析

#### 1.String

> **常用命令:**  set,get,decr,incr,mget 等。

String数据结构是简单的key-value类型，value其实不仅可以是String，也可以是数字。 
常规key-value缓存应用； 
常规计数：微博数，粉丝数等。

#### 2.Hash
> **常用命令：** hget,hset,hgetall 等。

hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象，后续操作的时候，你可以直接仅仅修改这个对象中的某个字段的值。 比如我们可以 hash 数据结构来存储用户信息，商品信息等等。比如下面我就用 hash 类型存放了我本人的一些信息：

```
key=JavaUser293847
value={
  “id”: 1,
  “name”: “SnailClimb”,
  “age”: 22,
  “location”: “Wuhan, Hubei”
}

```


#### 3.List
> **常用命令:** lpush,rpush,lpop,rpop,lrange等

list 就是链表，Redis list 的应用场景非常多，也是Redis最重要的数据结构之一，比如微博的关注列表，粉丝列表，消息列表等功能都可以用Redis的 list 结构来实现。

Redis list 的实现为一个双向链表，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销。

另外可以通过 lrange 命令，就是从某个元素开始读取多少个元素，可以基于 list 实现分页查询，这个很棒的一个功能，基于 redis 实现简单的高性能分页，可以做类似微博那种下拉不断分页的东西（一页一页的往下走），性能高。

#### 4.Set

> **常用命令：**
> sadd,spop,smembers,sunion 等

set 对外提供的功能与list类似是一个列表的功能，特殊之处在于 set 是可以自动排重的。

当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。可以基于 set 轻易实现交集、并集、差集的操作。

比如：在微博应用中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。Redis可以非常方便的实现如共同关注、共同粉丝、共同喜好等功能。这个过程也就是求交集的过程，具体命令如下：

```
sinterstore key1 key2 key3     将交集存在key1内
```

#### 5.Sorted Set
> **常用命令：** zadd,zrange,zrem,zcard等


和set相比，sorted set增加了一个权重参数score，使得集合中的元素能够按score进行有序排列。

**举例：** 在直播系统中，实时排行信息包含直播间在线用户列表，各种礼物排行榜，弹幕消息（可以理解为按消息维度的消息排行榜）等信息，适合使用 Redis 中的 Sorted Set 结构进行存储。








# 10.缓存雪崩和缓存穿透问题解决方案

## 10.1 缓存雪崩

简介：缓存同一时间大面积的失效，所以，后面的请求都会落到数据库上，造成数据库短时间内承受大量请求而崩掉。

解决办法（中华石杉老师在他的视频中提到过，视频地址在最后一个问题中有提到）：

- 事前：尽量保证整个 redis 集群的高可用性，发现机器宕机尽快补上。选择合适的内存淘汰策略。
- 事中：本地ehcache缓存 + hystrix限流&降级，避免MySQL崩掉
- 事后：利用 redis 持久化机制保存的数据尽快恢复缓存

![](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-9-25/6078367.jpg)


## 10.2 缓存穿透

简介：一般是黑客故意去请求缓存中不存在的数据，导致所有的请求都落到数据库上，造成数据库短时间内承受大量请求而崩掉。

解决办法： 有很多种方法可以有效地解决缓存穿透问题，最常见的则是采用布隆过滤器，将所有可能存在的数据哈希到一个足够大的bitmap中，一个一定不存在的数据会被 这个bitmap拦截掉，从而避免了对底层存储系统的查询压力。另外也有一个更为简单粗暴的方法（我们采用的就是这种），如果一个查询返回的数据为空（不管是数 据不存在，还是系统故障），我们仍然把这个空结果进行缓存，但它的过期时间会很短，最长不超过五分钟。

参考：

- [https://blog.csdn.net/zeb_perfect/article/details/54135506](https://blog.csdn.net/zeb_perfect/article/details/54135506)

# 11.如何解决 Redis 的并发竞争 Key 问题

所谓 Redis 的并发竞争 Key 的问题也就是多个系统同时对一个 key 进行操作，但是最后执行的顺序和我们期望的顺序不同，这样也就导致了结果的不同！

推荐一种方案：分布式锁（zookeeper 和 redis 都可以实现分布式锁）。（如果不存在 Redis 的并发竞争 Key 问题，不要使用分布式锁，这样会影响性能）

基于zookeeper临时有序节点可以实现的分布式锁。大致思想为：每个客户端对某个方法加锁时，在zookeeper上的与该方法对应的指定节点的目录下，生成一个唯一的瞬时有序节点。 判断是否获取锁的方式很简单，只需要判断有序节点中序号最小的一个。 当释放锁的时候，只需将这个瞬时节点删除即可。同时，其可以避免服务宕机导致的锁无法释放，而产生的死锁问题。完成业务流程后，删除对应的子节点释放锁。

在实践中，当然是从以可靠性为主。所以首推Zookeeper。

参考：

- https://www.jianshu.com/p/8bddd381de06

# 12.如何保证缓存与数据库双写时的数据一致性?

你只要用缓存，就可能会涉及到缓存与数据库双存储双写，你只要是双写，就一定会有数据一致性的问题，那么你如何解决一致性问题？

一般来说，就是如果你的系统不是严格要求缓存+数据库必须一致性的话，缓存可以稍微的跟数据库偶尔有不一致的情况，最好不要做这个方案，读请求和写请求串行化，串到一个内存队列里去，这样就可以保证一定不会出现不一致的情况

串行化之后，就会导致系统的吞吐量会大幅度的降低，用比正常情况下多几倍的机器去支撑线上的一个请求。



# Jedis操作Redis集群

```java
public class Demo {
    private static JedisCluster jedisCluster=null;
    private static Set<HostAndPort> hostAndPorts=null;

    public static  Set<HostAndPort> getHostAndPort(String hostAndPort){
        Set<HostAndPort> hap = new HashSet<HostAndPort>();
        String[] hosts = hostAndPort.split(",");
        String[] hs = null;
        for(String host:hosts){
            hs=host.split(":");
            hap.add(new HostAndPort(hs[0], Integer.parseInt(hs[1])));
        }
        return hap;
    }

    public static JedisCluster getJedisCluster(){
        GenericObjectPoolConfig gopc = new GenericObjectPoolConfig();
        gopc.setMaxTotal(32);
        gopc.setMaxIdle(4);
        gopc.setMaxWaitMillis(6000);
        hostAndPorts = getHostAndPort("192.168.0.100:7003,192.168.0.100:7004,192.168.0.100:7005,192.168.0.179:7000,192.168.0.179:7001,192.168.0.179:7002");
        jedisCluster = new JedisCluster(hostAndPorts, 2000, 2000, 3,null,gopc);
        return jedisCluster;
    }

    public static void main(String[] args) {
        jedisCluster = getJedisCluster();
        jedisCluster.set("name", "anthony");
        System.out.println(jedisCluster.get("name"));
    }
}
```



# Redis-Java操作

1.
```java
public static void deleteRedisStoreInformationByToken(String token,RedisTemplate<String,String> redisTemplate) {
        String userid = token.split("_")[0];
        Set<String> keys = redisTemplate.opsForValue().getOperations().keys(RedisKeyConstant.STOREINFOMATION + userid+"*");
        for (String string : keys) {
            redisTemplate.opsForValue().getOperations().delete(string);// 删除缓存中的token
    }
}
```


```java
//模糊模糊查询redis的keys
Set<String> keys=redisTemplate.keys(RedisKeyPrefix.GAME_SERVICE_GAME_STATUS +tbGameSiteVo.getSiteId()+":"+"*");
//获取redis的value值
List<String>value=redisTemplate.opsForValue().multiGet(keys);
//获取游戏的在线人数
int number=0;
//获取游戏的在线人数
try {
   Long onLineSum = value.stream()
       .map(l -> JSON.parseObject(l, UserGameInputVo.class))
       .filter(vo -> vo.getOnlineStatus() == 1)
       .filter(vo -> vo.getGameStatus() ==Integer.valueOf(tbGameSiteVo.getGameId()))
       .count();//统计各游戏的在线人数
   if(onLineSum==null){
      onLineSum=0L;
   }
   if(onLineSum!=null) {
      number = onLineSum.intValue();
   }
}catch (Exception e){

   log.error("在线人数为空"+e.getMessage());
}
```

