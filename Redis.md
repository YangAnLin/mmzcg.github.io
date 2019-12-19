[toc]


## Docker-Redis单机安装

```shell
docker run \
       -p 6699:6379 \
       --name myredis \
       -v $PWD/redis.conf:/etc/redis/redis.conf \
       -v $PWD/data:/data \
       -d redis:3.2 redis-server /etc/redis/redis.conf \
       --appendonly yes
```

命令说明：
* `--name myredis` : 指定容器名称，这个最好加上，不然在看docker进程的时候会很尴尬。
* `-p 6699:6379` ： 端口映射，默认redis启动的是6379,外部端口(6699)。
* `-v $PWD/redis.conf:/etc/redis/redis.conf` ： 将主机中当前目录下的redis.conf配置文件映射。
* `-v $PWD/data:/data -d redis:latest`： 将主机中当前目录下的data挂载到容器的/data
* `--redis-server --appendonly yes` :在容器执行redis-server启动命令，并打开redis持久化配置\
* 注意事项：
  * 如果不需要指定配置，`-v $PWD/redis.conf:/etc/redis/redis.conf` 可以不用 
  * redis-server 后面的那段 `/etc/redis/redis.conf` 也可以不用。
  * `$PWD` 在window系统下貌似不能用,可以用相对路径`/`

客户端连接

```shell
# 先查询到myredis容器的ip地址。
docker inspect myredis | grep IP 
# 连接到redis容器。然后就进入redis命令行了。
docker run -it redis:latest redis-cli -h 192.168.42.32 
```

## Centos-Redis源码集群安装
### 1.配置
用两台虚拟机模拟6个节点，一台机器3个节点，创建出3 master、3 salve 环境。

| worker1       | worker2 |
| ------------- | ------- |
| 192.168.0.179 | 7000    |
| 192.168.0.179 | 7001    |
| 192.168.0.179 | 7002    |
| 192.168.0.100 | 7003    |
| 192.168.0.100 | 7004    |
| 192.168.0.100 | 7005    |

### 2.下载,解压,编译安装
在worker1上
```shell
cd /usr/local
wget http://download.redis.io/releases/redis-5.0.5.tar.gz
tar -zxvf redis-5.0.5.tar.gz
cd redis-5.0.5
make && make instal
```
注意make的时候可能会报错,
```shell
yum install gcc
make MALLOC=libc
```

### 3.创建节点,在worker1
创建文件
```shell
cd /usr/local
mkdir redis_cluster
cd redis_cluster
mkdir 7000
mkdir 7001
mkdir 7002
cp /usr/local/redis-5.0.5/redis.conf /usr/local/redis_cluster/7000/
cp /usr/local/redis-5.0.5/redis.conf /usr/local/redis_cluster/7001/
cp /usr/local/redis-5.0.5/redis.conf /usr/local/redis_cluster/7002/
```

分别修改三个文件夹里的配置文件,修改如下内容
```conf
port  7000      //端口7000,7002,7003       
bind 0.0.0.0     //自己建议修改为0.0.0.0
daemonize yes   //redis后台运行
pidfile  /var/run/redis_7000.pid    //pidfile文件对应7000,7001,7002
cluster-enabled  yes    //开启集群  把注释#去掉
cluster-config-file  nodes_7000.conf   //集群的配置,配置文件首次启动自动生成7000,7001,7002
cluster-node-timeout  15000  //请求超时  默认15秒，可自行设置
appendonly  yes //aof日志开启 
```

启动节点的redis
`/usr/local/bin/redis-server` 这是在`make & make install`生成的
```shell
/usr/local/bin/redis-server redis_cluster/7000/redis.conf
/usr/local/bin/redis-server redis_cluster/7001/redis.conf
/usr/local/bin/redis-server redis_cluster/7002/redis.conf
```

检查 redis 启动情况
```shell
ps -ef | grep redi
root      61020      1  0 02:14 ?        00:00:01 redis-server 0.0.0.0:7000 [cluster]   
root      61024      1  0 02:14 ?        00:00:01 redis-server 0.0.0.0:7001 [cluster]   
root      61029      1  0 02:14 ?        00:00:01 redis-server 0.0.0.0:7002 [cluster]
```
### 4.再在worker2 上做相同的操作

### 5.启动集群
装的redis是5.x的版本,这里没有应用到`redis-trib.rb`,所以就不需要装ruby
```shell
cd /usr/local/bin
redis-cli --cluster create 192.168.0.100:7003 192.168.0.100:7004 192.168.0.100:7005 192.168.0.179:7000 192.168.0.179:7001 192.168.0.179:7002 --cluster-replicas 1
```

遇到
```shell
Can I set the above configuration? (type 'yes' to accept): yes
```
如数`yes`

### 6.校验,等运行完成
`redis-cli --cluster check 192.168.0.179:7000`
```
[root@worker1 src]# redis-cli --cluster check 192.168.0.179:7000
192.168.0.179:7000 (27bce53b...) -> 0 keys | 5462 slots | 1 slaves.
192.168.0.100:7004 (6b0173d9...) -> 0 keys | 5461 slots | 1 slaves.
192.168.0.100:7003 (9f15a932...) -> 0 keys | 5461 slots | 1 slaves.
[OK] 0 keys in 3 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 192.168.0.179:7000)
M: 27bce53bda92341ca4a5c82c2361ab99f24c0b27 192.168.0.179:7000
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: c7ebcd900fb7d9afb1980797acba45518cb7d877 192.168.0.100:7005
   slots: (0 slots) slave
   replicates 27bce53bda92341ca4a5c82c2361ab99f24c0b27
S: ed5256f8db1bf556a8dadbe8f2b07699507e17d9 192.168.0.179:7001
   slots: (0 slots) slave
   replicates 6b0173d925f70807a9081b7bc09bcd37be857342
S: 758609eaea88bac25b864f2badbab2171a68089b 192.168.0.179:7002
   slots: (0 slots) slave
   replicates 9f15a9329a9d0ec5c7fcb5abbba817730f0942f9
M: 6b0173d925f70807a9081b7bc09bcd37be857342 192.168.0.100:7004
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
M: 9f15a9329a9d0ec5c7fcb5abbba817730f0942f9 192.168.0.100:7003
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

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

## Windows安装包安装redis
下载: https://github.com/MSOpenTech/redis/releases  
可视化软件: Redis Desktop Manager

## Windows源码安装redis
启动redis:redis-server --service-start
卸载redis:redis-server --service-uninstall
安装成服务:redis-server --service-install redis.windows.conf



# Redis原理

# 1.Redis简介

* Redis 的数据是存在内存中的，所以读写速度非常快
* Redis 也经常用来做分布式锁。
* Redis 提供了多种数据类型来支持不同的业务场景。
* Redis 支持事务,持久化,LUA脚本,LRU驱动事件,多种集群方案。 

# 2.为什么要用 Redis/为什么要用缓存

**高性能：**

假如用户第一次访问数据库中的某些数据。这个过程会比较慢，因为是从硬盘上读取的。将该用户访问的数据存在数缓存中，这样下一次再访问这些数据的时候就可以直接从缓存中获取了。操作缓存就是直接操作内存，所以速度相当快。如果数据库中的对应数据改变的之后，同步改变缓存中相应的数据即可！

![](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-9-24/54316596.jpg)


**高并发：**

直接操作缓存能够承受的请求是远远大于直接访问数据库的，所以我们可以考虑把数据库中的部分数据转移到缓存中去，这样用户的一部分请求会直接到缓存这里而不用经过数据库。

![](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-9-24/85146760.jpg)

# 3.为什么要用Redis而不用 map/guava 做缓存?

* 缓存分为本地缓存和分布式缓存。以 Java 为例，使用自带的 map或者guava实现的是本地缓存，最主要的特点是轻量以及快速，生命周期随着 jvm 的销毁而结束，并且在多实例的情况下，每个实例都需要各自保存一份缓存，缓存不具有一致性

* 使用 redis 或 memcached 之类的称为分布式缓存，在多实例的情况下，各实例共用一份缓存数据，缓存具有一致性。缺点是需要保持 redis 或  memcached服务的高可用(需要客户端支持)，整个程序架构上较为复杂。

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


# 6.Redis 设置过期时间
```java
jedis.setex("myexpire", 20, "ceshi ");
```

如果设置了一批 key 只能存活1个小时，那么接下来1小时后，redis是怎么对这批key进行删除的？
redis使用的策略:**定期删除+惰性删除。**

* **定期删除**：redis默认是每隔 100ms 就**随机抽取**一些设置了过期时间的key，检查其是否过期，如果过期就删除。注意这里是随机抽取的。为什么要随机呢？你想一想假如 redis 存了几十万个 key ，每隔100ms就遍历所有的设置过期时间的 key 的话，就会给 CPU 带来很大的负载！
* **惰性删除** ：定期删除可能会导致很多过期 key 到了时间并没有被删除掉。所以就有了惰性删除。假如你的过期 key，靠定期删除没有被删除掉，还停留在内存里，除非你的系统去查一下那个 key，才会被redis给删除掉。这就是所谓的惰性删除，也是够懒的哈！
* **立即删除**: 在设置键的过期时间时，创建一个回调事件，当过期时间达到时，由时间处理器自动执行键的删除操作。目前redis事件处理器对时间事件的处理方式--无序链表，查找一个key的时间复杂度为O(n),所以并不适合用来处理大量的时间事件。


但是仅仅通过设置过期时间还是有问题的。我们想一下：如果定期删除漏掉了很多过期 key，然后你也没及时去查，也就没走惰性删除，此时会怎么样？如果大量过期key堆积在内存里，导致redis内存块耗尽了。怎么解决这个问题呢？ **redis 内存淘汰机制。**

# 7.Redis内存淘汰机制(MySQL里有2000w数据，Redis中只存20w的数据，如何保证Redis中的数据都是热点数据?)

redis 配置文件 [http://download.redis.io/redis-stable/redis.conf](http://download.redis.io/redis-stable/redis.conf)

**redis 提供 6种数据淘汰策略：**

| 序号 | 属性            | 说明                                                         | 版本      |
| ---- | --------------- | ------------------------------------------------------------ | --------- |
| 1    | volatile-lru    | 从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰 |           |
| 2    | volatile-ttl    | 从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰 |           |
| 3    | volatile-random | 从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰 |           |
| 4    | allkeys-lru     | 当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的key **（这个是最常用的）** |           |
| 5    | allkeys-random  | 从数据集（server.db[i].dict）中任意选择数据淘汰              |           |
| 6    | no-eviction     | 禁止驱逐数据，也就是说当内存不足以容纳新写入数据时，新写入操作会报错。这个应该没人使用吧！ |           |
| 7    | volatile-lfu    | 从已设置过期时间的数据集(server.db[i].expires)中挑选最不经常使用的数据淘汰 | 4.0版本后 |
| 8    | allkeys-lfu     | 当内存不足以容纳新写入数据时，在键空间中，移除最不经常使用的key | 4.0版本后 |

# 8.redis 持久化机制(怎么保证 redis 挂掉之后再重启数据可以进行恢复)

很多时候我们需要持久化数据也就是将内存中的数据写入到硬盘里面，大部分原因是为了之后重用数据（比如重启机器、机器故障之后恢复数据），或者是为了防止系统故障而将数据备份到一个远程位置。

Redis不同于Memcached的很重一点就是，Redis支持持久化，而且支持两种不同的持久化操作。**Redis的一种持久化方式叫快照（snapshotting，RDB），另一种方式是只追加文件（append-only file,AOF）**。这两种方法各有千秋，下面我会详细这两种持久化方法是什么，怎么用，如何选择适合自己的持久化方法。

## 8.1 快照（snapshotting）持久化（RDB）

Redis创建快照之后，可以对快照进行备份，可以将快照复制到其他服务器从而创建具有相同数据的服务器副本（Redis主从结构，主要用来提高Redis性能），还可以将快照留在原地以便重启服务器的时候使用。

快照持久化是Redis默认采用的持久化方式，在redis.conf配置文件中默认有此下配置：
```conf
save 900 1           #在900秒(15分钟)之后，如果至少有1个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
save 300 10          #在300秒(5分钟)之后，如果至少有10个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
save 60 10000        #在60秒(1分钟)之后，如果至少有10000个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
```

* 根据配置，快照将被写入dbfilename选项指定的文件里面，并存储在dir选项指定的路径上面。
* **如果在新的快照文件创建完毕之前，Redis、系统或者硬件这三者中的任意一个崩溃了，那么Redis将丢失最近一次创建快照写入的所有数据。**

举个例子：
```conf
假设Redis的上一个快照是2：35开始创建的，并且已经创建成功。

下午3：06时，Redis又开始创建新的快照，并且在下午3：08快照创建完毕之前，有35个键进行了更新。

如果在下午3：06到3：08期间，系统发生了崩溃，导致Redis无法完成新快照的创建工作，那么Redis将丢失下午2：35之后写入的所有数据。

另一方面，如果系统恰好在新的快照文件创建完毕之后崩溃，那么Redis将丢失35个键的更新数据。
```

**创建快照的办法有如下几种：**

- **BGSAVE命令：** 客户端向Redis发送 **BGSAVE命令** 来创建一个快照。对于支持BGSAVE命令的平台来说（基本上所有平台支持，除了Windows平台），Redis会调用fork来创建一个子进程，然后子进程负责将快照写入硬盘，而父进程则继续处理命令请求。
- **SAVE命令：** 客户端还可以向Redis发送 **SAVE命令** 来创建一个快照，接到SAVE命令的Redis服务器在快照创建完毕之前不会再响应任何其他命令。SAVE命令不常用，我们通常只会在没有足够内存去执行BGSAVE命令的情况下，又或者即使等待持久化操作执行完毕也无所谓的情况下，才会使用这个命令。
- **save选项：** 如果用户设置了save选项（一般会默认设置），比如 **save 60 10000**，那么从Redis最近一次创建快照之后开始算起，当“60秒之内有10000次写入”这个条件被满足时，Redis就会自动触发BGSAVE命令。
- **SHUTDOWN命令：**  当Redis通过SHUTDOWN命令接收到关闭服务器的请求时，或者接收到标准TERM信号时，会执行一个SAVE命令，阻塞所有客户端，不再执行客户端发送的任何命令，并在SAVE命令执行完毕之后关闭服务器。
- **一个Redis服务器连接到另一个Redis服务器：** 当一个Redis服务器连接到另一个Redis服务器，并向对方发送SYNC命令来开始一次复制操作的时候，如果主服务器目前没有执行BGSAVE操作，或者主服务器并非刚刚执行完BGSAVE操作，那么主服务器就会执行BGSAVE命令

如果系统真的发生崩溃，用户将丢失最近一次生成快照之后更改的所有数据。因此，快照持久化只适用于即使丢失一部分数据也不会造成一些大问题的应用程序。不能接受这个缺点的话，可以考虑AOF持久化。

## 8.2 AOF（append-only file）持久化

与快照持久化相比，AOF持久化 的实时性更好，因此已成为主流的持久化方案。默认情况下Redis没有开启AOF（append only file）方式的持久化，可以通过appendonly参数开启：

```conf
appendonly yes
```

开启AOF持久化后每执行一条会更改Redis中的数据的命令，Redis就会将该命令写入硬盘中的AOF文件。AOF文件的保存位置和RDB文件的位置相同，都是通过dir参数设置的，默认的文件名是appendonly.aof

在Redis的配置文件中存在三种不同的 AOF 持久化方式，它们分别是：

```conf
appendfsync always    #每次有数据修改发生时都会写入AOF文件,这样会严重降低Redis的速度
appendfsync everysec  #每秒钟同步一次，显示地将多个写命令同步到硬盘 **(默认的)**
appendfsync no        #让操作系统决定何时进行同步
```

* 为了兼顾数据和写入性能，用户可以考虑 appendfsync everysec选项 ，让Redis每秒同步一次AOF文件，Redis性能几乎没受到任何影响。而且这样即使出现系统崩溃，用户最多只会丢失一秒之内产生的数据。当硬盘忙于执行写入操作的时候，Redis还会优雅的放慢自己的速度以便适应硬盘的最大写入速度。
* 虽然AOF持久化非常灵活地提供了多种不同的选项来满足不同应用程序对数据安全的不同要求，但AOF持久化也有缺陷——AOF文件的体积太大

## 8.3 Redis 4.0 对于持久化机制的优化

Redis 4.0 开始支持 RDB 和 AOF 的混合持久化（默认关闭，可以通过配置项 `aof-use-rdb-preamble` 开启）。

如果把混合持久化打开，AOF 重写的时候就直接把 RDB 的内容写到 AOF 文件开头。这样做的好处是可以结合 RDB 和 AOF 的优点, 快速加载同时避免丢失过多的数据。当然缺点也是有的， AOF 里面的 RDB 部分是压缩格式不再是 AOF 格式，可读性较差。

## 8.3 AOF重写

AOF虽然在某个角度可以将数据丢失降低到最小而且对性能影响也很小，但是极端的情况下，体积不断增大的AOF文件很可能会用完硬盘空间。另外，如果AOF体积过大，那么还原操作执行时间就可能会非常长。

为了解决AOF体积过大的问题，用户可以向Redis发送 **BGREWRITEAOF命令** ，这个命令会通过`移除`AOF文件中的`冗余命令`来重写（rewrite）AOF文件来减小AOF文件的体积。

BGREWRITEAOF命令和BGSAVE创建快照原理十分相似，所以AOF文件重写也需要用到子进程，这样会导致性能问题和内存占用问题，和快照持久化一样。更糟糕的是，如果不加以控制的话，AOF文件的体积可能会比快照文件大好几倍。

AOF重写可以产生一个新的AOF文件，这个新的AOF文件和原有的AOF文件所保存的数据库状态一样，但体积更小。

AOF持久化也可以通过设置(默认是这样的)
```conf
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
```
举例：假设用户对Redis设置了如上配置选项并且启用了AOF持久化。那么当AOF文件体积大于64mb，并且AOF的体积比上一次重写之后的体积大了至少一倍（100%）的时候，Redis将执行BGREWRITEAOF命令。

无论是AOF持久化还是快照持久化，将数据持久化到硬盘上都是非常有必要的，但除了进行持久化外，用户还必须对持久化得到的文件进行备份（最好是备份到不同的地方），这样才能尽量避免数据丢失事故发生。如果条件允许的话，最好能将快照文件和重新重写的AOF文件备份到不同的服务器上面。

随着负载量的上升，或者数据的完整性变得 越来越重要时，用户可能需要使用到复制特性。


# 9.Redis 事务

Redis 通过 MULTI、EXEC、WATCH 等命令来实现事务(transaction)功能。事务提供了一种将多个命令请求打包，然后一次性、按顺序地执行多个命令的机制，并且在事务执行期间，服务器不会中断事务而改去执行其他客户端的命令请求，它会将事务中的所有命令都执行完毕，然后才去处理其他客户端的命令请求。

在传统的关系式数据库中，常常用 ACID 性质来检验事务功能的可靠性和安全性。在 Redis 中，事务总是具有原子性（Atomicity）、一致性（Consistency）和隔离性（Isolation），并且当 Redis 运行在某种特定的持久化模式下时，事务也具有持久性（Durability）。

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

