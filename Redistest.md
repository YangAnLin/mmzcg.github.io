# Redis的特性

1. 数据间没有必然的关联关系
2. 内部采用单线程机制进行工作
3. 高性能。官方提供测试数据，50个并发执行100000个请求，读的速度是110000次/s，写的速度是81000次/s。
4. 多数据类型支持：string（字符串类型）、list（列表类型）、hash（散列类型）、set（集合类型）、sorted_set（有序集合类型）
5. 持久化支持。可以进行数据灾难恢复



设值

set key value

帮助

help get

# redis的数据类型

redis自身是一个Map,其中所有的数据都是采用key:value的形式存储

数据类型指的是存储的数据的类型，也就是value部分的类型，key部分永远都是字符串

- string --> String
- hash --> Hashmap
- list --> LinkList
- set --> HashSet
- sorted_set --> TreeSet

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409191636914.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FtcW0zMw==,size_16,color_FFFFFF,t_70)





## String

- 存储的数据：单个数据，最简单的数据存储类型，也是最常用的数据存储类型
- 存储数据的格式：一个存储空间保存一个数据
- 存储内容：通常使用字符串，如果字符串以整数的形式展示，可以作为数字操作使用,最后实际上还是字符串

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409191821527.png)



```shell
# 设值
192.168.245.129:0>set k1 anthony
"OK"

# 取值
192.168.245.129:0>get k1
"anthony"

# 删除值,成功返回1
192.168.245.129:0>del k1
"1"

# 删除值,不成功返回0
192.168.245.129:0>del k1234
"0"

# 一次性存入多个值
192.168.245.129:0>mset k1 v1 k2 v2 k3 v3
"OK"

# 一次性取出多个值
192.168.245.129:0>mget k1 k2 k3
 1)  "v1"
 2)  "v2"
 3)  "v3"
192.168.245.129:0>

# 打印值的长度
192.168.245.129:1>set name anthony
"OK"
192.168.245.129:1>get name
"anthony"
192.168.245.129:1>strlen name
"7"
192.168.245.129:1>

# 追加
# 有数据就追加
192.168.245.129:1>append name 666
"10"
192.168.245.129:1>get name
"anthony666"
# 没数据就新建
192.168.245.129:1>append othername frankie
"7"
192.168.245.129:1>get othername
"frankie"
192.168.245.129:1>

# 递增递减,小数不行
192.168.245.129:1>set num 1
"OK"
192.168.245.129:1>incr num
"2"
192.168.245.129:1>incr num
"3"
192.168.245.129:1>decr num
"2"
192.168.245.129:1>decr num
"1"

# 递增递减指定值,小数不行
192.168.245.129:1>incrby num 4
"6"
192.168.245.129:1>decrby num 2
"4"
# 小数不行
192.168.245.129:1>incrby num 1.5
"ERR value is not an integer or out of range"

# 递增指定小数,貌似没有递减
192.168.245.129:1>incrbyfloat num 1.5
"5.5"

# 指定生命周期  setex key seconds value
# setex 秒
# psetex 毫秒
192.168.245.129:1>setex phone2 2 1392465115
"OK"
192.168.245.129:1>get phone2
null
```

- string在redis内部存储默认就是一个字符串，当遇到增减类操作incr,decr时会转成数值型进行计算
- redis所有的操作都是原子性的，采用单线程处理所有业务，命令是一个一个执行的，因此无需考虑并发带来的数据影响。
- 注意：**按数值进行操作的数据，如果原始数据不能转成数值，或超过了redis数值上线范围，将会报错**。9223372036854775807 (java中long型数据最大值，Long.MAX_VALUE)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200413220144901.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FtcW0zMw==,size_16,color_FFFFFF,t_70)

数据库中的热点数据key命名惯例



![在这里插入图片描述](https://img-blog.csdnimg.cn/20200413220259916.png)

## Hash

- 新的存储需求：对一系列存储的数据进行编组，方便管理，典型应用存储对象信息

- 需要的内存结构：一个存储空间保存多少个键值对数据

- hash类型：底层使用哈希表结构实现数据存

  

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200413220558738.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FtcW0zMw==,size_16,color_FFFFFF,t_70)

```shell
# 设值/修改值  hset key filed1 value
192.168.245.129:0>HSET user name zhangsan
"1"
192.168.245.129:0>hset user age 38
"1"

# 取一个属性值
192.168.245.129:0>hget user age
"38"

# 取多个属性值
192.168.245.129:0>hmget user age name
 1)  "45"
 2)  "zhangsan"

# 取一个key
192.168.245.129:0>hgetall user
 1)  "name"
 2)  "zhangsan"
 3)  "age"
 4)  "38"
 
 # 删除一个属性值
192.168.245.129:0>hdel user name
"1"
192.168.245.129:0>hgetall user
 1)  "age"
 2)  "38"
 
 # 查看有多少个属性
 192.168.245.129:0>hlen user
"3"

# 获取所有的属性
192.168.245.129:0>hkeys user
 1)  "age"
 2)  "name"
 3)  "sex"
 
# 获取所有的属性值
192.168.245.129:0>hvals user
 1)  "45"
 2)  "zhangsan"
 3)  "n"
 
 # 指定属性增加指定值
 192.168.245.129:0>hincrby user age 1
"46"
```

## List

数据存储需求：存储多个数据，并对数据进入存储空间的顺序进行区分
需要的存储数据：一个存储空间保存多个数据，且通过数据可以体现进入顺序
list类型：保存多个数据，底层使用双向链表存储结构实现

![image-20200806002003265](C:\Users\y9507\AppData\Roaming\Typora\typora-user-images\image-20200806002003265.png)

```shell
# 先插入huawei
192.168.245.129:0>lpush list1 huawei
"1"
# 再插入apple
192.168.245.129:0>lpush list1 apple
"2"
# 最后插入Microsoft
192.168.245.129:0>lpush list1 microsoft
"3"
# 从左边取
192.168.245.129:0>lrange list1 0 2
 1)  "microsoft"
 2)  "apple"
 3)  "huawei"
 
 # 一次性插入多条数据
192.168.245.129:0>rpush list2 a b c
"3"

#  从左边取
# lrange key start stop
# lindex key index 取指定索引的值
# llen key 取长度
# 没有rrange
192.168.245.129:0>lrange list2 0 2
 1)  "a"
 2)  "b"
 3)  "c"
 # 从左边取的第二钟方法
192.168.245.129:0>lrange list2 0 -1
 1)  "a"
 2)  "b"
 3)  "c"
 
# lpop从左边删,rpop从右边删 
192.168.245.129:0>lpush list3 a b c
"3"
192.168.245.129:0>lpop list3
"c"

# 阻塞取值,从运行命令开始,如果有数据,取出来,立马返回,如果没有数据,就等指定的时间20s,有就立马返回结束,如果没有,就一直等到时间结束
192.168.245.129:0>blpop list4 20
 1)  "list4"
 2)  "32"

# 删除指定数据
# lrem key count value
# count是删除多少个value
192.168.245.129:0>lpush dianzan  a b c d
"4"
192.168.245.129:0>lrem dianzan 1 c
"1"
192.168.245.129:0>lrange dianzan 0 -1
 1)  "d"
 2)  "b"
 3)  "a"
```

## Set

- list 中保存的数据都是string类型的，数据总容量式由西安的，最多232-1个元素（4294967295）
- list具有索引的概念，但是操作数据时候通常以队列的形式进行入队出队操作，或以栈的形式进入栈出栈的操作
- 获取全部数据操作结束索引设置为-1
- list 可以对数据进行分页操作，通过第一页的信息来自list，第2页及更多的信息通过数据库的形式加载

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420113858892.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FtcW0zMw==,size_16,color_FFFFFF,t_70)

```shell
# 添加
192.168.245.129:0>sadd users zs
"1"
192.168.245.129:0>sadd users lisi
"1"
192.168.245.129:0>sadd users ww
"1"

# 查列表
192.168.245.129:0>smembers users
 1)  "lisi"
 2)  "ww"
 3)  "zs"
 
 # 查数量
192.168.245.129:0>scard users
"3"

# 判断是否有指定数据
192.168.245.129:0>sismember users ls
"0"
192.168.245.129:0>sismember users ww
"1"
192.168.245.129:0>smembers users
 1)  "lisi"
 2)  "ww"
 3)  "zs"
 
 # 删除指定数据
192.168.245.129:0>srem users ww
"1"
192.168.245.129:0>smembers users
 1)  "lisi"
 2)  "zs"
```

# Key 通用命令

```shell
-------------------------------------基本操作
# 删除
del key

# 获取key是否存在
exists key

# 获取key的类型
type key


-----------------------------有效期

# 设置指定有效期
# 秒
expire key second 
# 毫秒
pexpire key milliseconds 
# 有效期是时间戳
expireat key timestamp

# 获取key的有效期
ttl key
pttl key  毫秒

# 切换key从时效性变成永久性
persist key


-------------------------------------查询
# 具体看图片
keys pattern


-------------------------------------其它操作
# 重命名
# 不判断newkey存不存在,newkey就用key的值覆盖,再改成
rename key newkey

# 如果newkey 存在,就不能改
rename key newkey

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420144325336.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FtcW0zMw==,size_16,color_FFFFFF,t_70)

# 安装redis

过滤注释

 cat redis.conf |grep -v '#' |grep -v '^$' >redis-6237.conf

# 持久化

## RDB(快照)

save 会生成rdb文件

##### RDB启动方式——save指令相关配置

- dbfilename dump.rdb
  说明：设置本地数据库文件名，默认值为dump.rdb
  经验：通常设置为dump-端口号.rdb
- dir
  说明：设置存储.rdb文件的路径
  经验：通常设置成存储空间较大的目录中，目录名称data
- rdbcompression yes
  说明：设置存储至本地数据库时是否压缩数据，默认为yes，采用LZF压缩
  经验：通常默认为开启状态，如果设置成no，可以节省CPU运行时间，但会使存储的文件变大（巨大）
- rdbchecksumy yes
  说明：设置是否进行RDB文件格式的校验，该校验过程在写文件和读文件过程均进行
  经验：通常默认为开启状态，如果设置为no，可以节约读写性过程约10%时间消耗，但是存储一定的数据损坏风险

注意：**Redis是单线程的**，所有命令都会在类似队列中排好队，不建议使用save指令，因为save指令的执行会阻塞当前Redis服务器，直到当前RDB过程完成位置，有可能会造成长时间阻塞，**线上环境不建议使用**

##### bgsave指令