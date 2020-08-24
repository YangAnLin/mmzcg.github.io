[toc]

# Mysql安装

```shell
# 卸载旧版本
# 停掉服务 service mysqld stop
# 确保所有msyql的服务进程杀死
ps -ef | grep -i mysql 查出mysql的相关进程
# 然后一个一个地 kill -9 进程号
# 删除mysql的rpm包
rpm -qa | grep -i mysql | xargs rpm -e --nodeps
# 删除mysql遗留的文件
find / -name mysql | xargs rm -rf
find / -name my.cnf | xargs rm -rf

# 下载 mysql 源安装包
curl -LO http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm

# 安装 mysql 源
sudo yum localinstall mysql57-community-release-el7-11.noarch.rpm

# 检查 yum 源是否安装成功
$ sudo yum repolist enabled | grep "mysql.*-community.*"
mysql-connectors-community           MySQL Connectors Community              21
mysql-tools-community                MySQL Tools Community                   38
mysql57-community                    MySQL 5.7 Community Server             130

# 安装
sudo yum install mysql-community-server

# 启动服务
sudo systemctl enable mysqld
sudo systemctl start mysqld

# 查看服务状态
sudo systemctl status mysqld

# 查看root默认密码
$ grep 'temporary password' /var/log/mysqld.log
[Note] A temporary password is generated for root@localhost: **********

# 登录
$ mysql -u root -p
Enter password: 
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';

# MySQL 环境变量可以查看密码策略的相关信息：
mysql> SHOW VARIABLES LIKE 'validate_password%';
+--------------------------------------+--------+
| Variable_name                        | Value  |
+--------------------------------------+--------+
| validate_password_check_user_name    | OFF    |
| validate_password_dictionary_file    |        |
| validate_password_length             | 8      |
| validate_password_mixed_case_count   | 1      |
| validate_password_number_count       | 1      |
| validate_password_policy             | MEDIUM |
| validate_password_special_char_count | 1      |
+--------------------------------------+--------+
7 rows in set (0.01 sec)

# 指定密码校验策略
$ sudo vi /etc/my.cnf

[mysqld]
# 添加如下键值对, 0=LOW, 1=MEDIUM, 2=STRONG
validate_password_policy=0

# 禁用密码策略
$ sudo vi /etc/my.cnf
	
[mysqld]
# 禁用密码校验策略
validate_password = off

# 重启 MySQL 服务，使配置生效
$ sudo systemctl restart mysqld

# 开启防火区3306端口
$ sudo firewall-cmd --zone=public --add-port=3306/tcp --permanent
$ sudo firewall-cmd --reload

# 配置默认编码为 utf8
$ vi /etc/my.cnf
[mysqld]
# 在myslqd下添加如下键值对
character_set_server=utf8
init_connect='SET NAMES utf8'

# 添加远程登录用户
mysql> GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' IDENTIFIED BY 'secret' WITH GRANT OPTION;
```

# Mysql基本命令

展示

```sql
可以使用`;`,`\g`,(水平展示)`\G`(垂直展示)
```

插入数据:

```sql
INSERT INTO student VALUES(1,'eric',20,'广州人','男');
```

修改数据:  

```sql
UPDATE student SET gender='女' WHERE id=2;
```

删除数据

```sql
 DELETE FROM student WHERE id=3;
```

去重复

```sql
SELECT DISTINCT 字段名 FROM student;
```

group by

```sql
select count(a),c from test group by c
```

左链接

```sql
select * from test1 a left join test2 b on a.id=b.id
```

切换成指定库名

```sql
use company(库名);
```

查看现在正在连接的库名

```sql
select  database();
```

查看所有的,我有权访问的的库名

```sql
show databases;
```

设置隔离级别(8.0版本)

```sql
// 设置隔离级别,当前session
mysql> set session transaction isolation level read uncommitted;
// 设置隔离级别,全局
mysql> SET global  transaction isolation level read uncommitted;
// 查询隔离级别
mysql> select @@global.transaction_isolation,@@transaction_isolation;
```

## 六种关联查询

交叉连接(笛卡尔积):

## 什么是子查询

1. 条件：一条SQL语句的查询结果做为另一条查询语句的条件或查询结果
2. 嵌套：多条SQL语句嵌套使用，内部的SQL查询语句称为子查询。

## Mysql中 in 和 exists 区别

mysql中的in语句是把外表和内表作hash 连接，而exists语句是对外表作loop循环，每次loop循环再对内表进行查询。一直大家都认为exists比in语句的效率要高，这种说法其实是不准确的。这个是要区分环境的。

1. 如果查询的两个表大小相当，那么用in和exists差别不大。
2. 如果两个表中一个较小，一个是大表，则子查询表大的用exists，子查询表小的用in。
3. not in 和not exists：如果查询语句使用了not in，那么内外表都进行全表扫描，没有用到索引；而not extsts的子查询依然能用到表上的索引。所以无论那个表大，用not exists都比not in要快。

# 数据类型

## 整数类型

包括TINYINT、SMALLINT、MEDIUMINT、INT、BIGINT，分别表示1字节、2字节、3字节、4字节、8字节整数。

任何整数类型都可以加上`UNSIGNED`属性，表示数据是无符号的，即非负整数。

`长度`：整数类型可以被指定长度，例如：INT(11)表示长度为11的INT类型。它不会限制值的合法范围，只会影响显示字符的个数，而且需要和UNSIGNED ZEROFILL属性配合使用才有意义。
`例子`，假定类型设定为INT(5)，属性为UNSIGNED ZEROFILL，如果用户插入的数据为12的话，那么数据库实际存储数据为00012。

### int(20)中20的涵义

是指显示字符的长度。20表示最大显示宽度为20，但仍占4字节存储，存储范围不变；

不影响内部存储，只是影响带 zerofill 定义的 int 时，前面补多少个 0，易于报表展示

## 实数类型

包括FLOAT、DOUBLE、DECIMAL。

DECIMAL可以用于存储比BIGINT还大的整型，能存储精确的小数。

FLOAT和DOUBLE是有取值范围的，并支持使用标准的浮点进行近似计算。

计算时FLOAT和DOUBLE相比DECIMAL效率更高一些，DECIMAL你可以理解成是用字符串进行处理。

## 字符串类型

包括VARCHAR、CHAR、TEXT、BLOB
VARCHAR用于存储可变长字符串，它比定长类型更节省空间。

VARCHAR使用额外1或2个字节存储字符串长度。列长度小于255字节时，使用1字节表示，否则使用2字节表示。

VARCHAR存储的内容超出设置的长度时，内容会被截断。

CHAR是定长的，根据定义的字符串长度分配足够的空间。

CHAR会根据需要使用空格进行填充方便比较。

CHAR适合存储很短的字符串，或者所有值都接近同一个长度。

CHAR存储的内容超出设置的长度时，内容同样会被截断

### varchar与char的区别

**char的特点**

- char表示定长字符串，长度是固定的；
- 如果插入数据的长度小于char的固定长度时，则用空格填充；
- 因为长度固定，所以存取速度要比varchar快很多，甚至能快50%，但正因为其长度固定，所以会占据多余的空间，是空间换时间的做法；
- 对于char来说，最多能存放的字符个数为255，和编码无关

**varchar的特点**

- varchar表示可变长字符串，长度是可变的；
- 插入的数据是多长，就按照多长来存储；
- varchar在存取方面与char相反，它存取慢，因为长度不固定，但正因如此，不占据多余的空间，是时间换空间的做法；
- 对于varchar来说，最多能存放的字符个数为65532

总之，结合性能角度（char更快）和节省磁盘空间角度（varchar更小），具体情况还需具体来设计数据库才是妥当的做法。

### varchar(50)中50的涵义

最多存放50个字符，varchar(50)和(200)存储hello所占空间一样，但后者在排序时会消耗更多内存，因为order by col采用fixed_length计算col长度(memory引擎也一样)。在早期 MySQL 版本中， 50 代表字节数，现在代表字符数。

## 使用策略

对于经常变更的数据来说，CHAR比VARCHAR更好，因为CHAR不容易产生碎片。

对于非常短的列，CHAR比VARCHAR在存储空间上更有效率。

使用时要注意只分配需要的空间，更长的列排序时会消耗更多内存。

尽量避免使用TEXT/BLOB类型，查询时会使用临时表，导致严重的性能开销。

## 枚举类型

把不重复的数据存储为一个预定义的集合。有时可以使用ENUM代替常用的字符串类型。

ENUM存储非常紧凑，会把列表值压缩到一个或两个字节。

ENUM在内部存储时，其实存的是整数。

尽量避免使用数字作为ENUM枚举的常量，因为容易混乱。

排序是按照内部存储的整数

## 日期和事件类型

用整数保存时间戳通常不方便处理。

如果需要存储微妙，可以使用bigint存储。

切记不要用字符串存储日期

* 字符串占用的空间更大！

* 字符串存储的日期比较效率比较低（逐个字符进行比对），无法用日期相关的 API 进行计算和比较。

|      | DateTime | Timestamp            |
| ---- | -------- | -------------------- |
| 空间 | 8个字节  | 4 个字节             |
| 时区 | 没有时区 | 随便时区的变化而变化 |
| 效率 | 低       | 高                   |

# 索引

## 什么是索引

索引是一种特殊的文件(InnoDB数据表上的索引是表空间的一个组成部分)，它们包含着对数据表里所有记录的引用指针。它是要占据物理空间的。

索引是一种数据结构。数据库索引，是数据库管理系统中一个`排序`的数据结构。索引的实现通常使用B树及其变种B+树。

## 索引优缺点

索引的优点

- 可以大大加快数据的检索速度，这也是创建索引的最主要的原因。
- 通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。

索引的缺点

- 时间方面：创建索引和维护索引要耗费时间，具体地，当对表中的数据进行增加、删除和修改的时候，索引也要动态的维护，会降低增/改/删的执行效率；
- 空间方面：索引需要占物理空间。

## 创建索引

```sql
-- 建表
CREATE TABLE user_index2 (
	id INT auto_increment PRIMARY KEY,
	first_name VARCHAR (16),
	last_name VARCHAR (16),
	id_card VARCHAR (18),
	information text,
	KEY name (first_name, last_name),
	FULLTEXT KEY (information),
	UNIQUE KEY (id_card)
);


-- ALTER TABLE用来创建普通索引、UNIQUE索引    或        PRIMARY KEY索引
ALTER TABLE table_name ADD INDEX index_name (column_list);

-- CREATE INDEX可对表增加普通索引或UNIQUE索引,但是,不能创建PRIMARY KEY索引
CREATE INDEX index_name ON table_name (column_list);
```

## 创建索引原则

* 最左前缀匹配原则，组合索引非常重要的原则，mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配，比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。
* 较频繁作为查询条件的字段才去创建索引
* 更新频繁字段不适合创建索引
* 若是不能有效区分数据的列不适合做索引列(如性别，男女未知，最多也就三种，区分度实在太低)
* 尽量的扩展索引，不要新建索引。比如表中已经有a的索引，现在要加(a,b)的索引，那么只需要修改原来的索引即可。
* 定义有外键的数据列一定要建立索引
* 对于那些查询中很少涉及的列，重复值比较多的列不要建立索引。
* 对于定义为text、image和bit的数据类型的列不要建立索引。
* 适合索引的列是出现在where子句中的列，或者连接子句中指定的列
* 基数较小的类，索引效果较差，没有必要在此列建立索引
* 使用短索引，如果对长字符串列进行索引，应该指定一个前缀长度，这样能够节省大量索引空间
* 不要过度索引。索引需要额外的磁盘空间，并降低写操作的性能。在修改表内容的时候，索引会进行更新甚至重构，索引列越多，这个时间就会越长。所以只保持需要的索引有利于查询即可。
* 非空字段：应该指定列为NOT NULL，除非你想存储NULL。在mysql中，含有空值的列很难进行查询优化，因为它们使得索引、索引的统计信息以及比较运算更加复杂。你应该用0、一个特殊的值或者一个空串代替空值；
* 取值离散大的字段：（变量各个取值之间的差异程度）的列放到联合索引的前面，可以通过count()函数查看字段的差异值，返回值越大说明字段的唯一值越多字段的离散程度高；
* 索引字段越小越好：数据库的数据存储以页为单位一页存储的数据越多一次IO操作获取的数据越大效率越高

## 删除索引

```sql
-- 删除普通索引
alter table table_name drop KEY name;
alter table table_name drop KEY id_card;
alter table table_name drop KEY information;

-- 删除主键索引,如果主键自增长，那么不能直接执行此操作（自增长依赖于主键索引),需要取消自增长再行删除
alter table 表名 drop primary key
```

## 索引类型

**主键索引:** 数据列不允许重复，不允许为NULL，一个表只能有一个主键。

**唯一索引:** 数据列不允许重复，允许为NULL值，一个表允许多个列创建唯一索引。

- `ALTER TABLE table_name ADD UNIQUE (column);` 创建唯一索引
- `ALTER TABLE table_name ADD UNIQUE (column1,column2);` 创建唯一组合索引

**普通索引:** 基本的索引类型，没有唯一性的限制，允许为NULL值。

- `ALTER TABLE table_name ADD INDEX index_name (column);`创建普通索引
- `ALTER TABLE table_name ADD INDEX index_name(column1, column2, column3);`创建组合索引

**全文索引：** 是目前搜索引擎使用的一种关键技术。

- 可以通过`ALTER TABLE table_name ADD FULLTEXT (column);`创建全文索引

## 索引使用场景

### where

根据`id`查询记录，因为`id`字段仅建立了主键索引，因此此SQL执行可选的索引只有主键索引，如果有多个，最终会选一个较优的作为检索的依据。

### order by

当我们使用`order by`将查询结果按照某个字段排序时，如果该字段没有建立索引，那么执行计划会将查询出的所有数据使用外部排序（将数据从硬盘分批读取到内存使用内部排序，最后合并排序结果），这个操作是很影响性能的，因为需要将查询涉及到的所有数据从磁盘中读到内存（如果单条数据过大或者数据量过多都会降低效率），更无论读到内存之后的排序了。

但是如果我们对该字段建立索引`alter table 表名 add index(字段名)`，那么由于索引本身是有序的，因此直接按照索引的顺序和映射关系逐条取出数据即可。而且如果分页的，那么只用**取出索引表某个范围内的索引对应的数据**，而不用像上述那**取出所有数据**进行排序再返回某个范围内的数据。（从磁盘取数据是最影响性能的）

### join

对`join`语句匹配关系（`on`）涉及的字段建立索引能够提高效率

### 索引覆盖

如果要查询的字段都建立过索引，那么引擎会直接在索引表中查询而不会访问原始数据（否则只要有一个字段没有建立索引就会做全表扫描），这叫索引覆盖。因此我们需要尽可能的在`select`后只写必要的查询字段，以增加索引覆盖的几率。

这里值得注意的是不要想着为每个字段建立索引，因为优先使用索引的优势就在于其体积小。

## 索引的数据结构

索引的数据结构和具体存储引擎的实现有关，在MySQL中使用较多的索引有**Hash索引**，**B+树索引**等，而我们经常使用的InnoDB存储引擎的默认索引实现为：B+树索引。对于哈希索引来说，底层的数据结构就是哈希表，因此在绝大多数需求为单条记录查询的时候，可以选择哈希索引，查询性能最快；其余大部分场景，建议选择BTree索引。

### B树索引

mysql通过存储引擎取数据，基本上90%的人用的就是InnoDB了，按照实现方式分，InnoDB的索引类型目前只有两种：BTREE（B树）索引和HASH索引。B树索引是Mysql数据库中使用最频繁的索引类型，基本所有存储引擎都支持BTree索引。通常我们说的索引不出意外指的就是（B树）索引（实际是用B+树实现的，因为在查看表索引时，mysql一律打印BTREE，所以简称为B树索引）

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOC85LzI0LzE2NjBjMGYxNGRhY2Y2ZjU?x-oss-process=image/format,png)

查询方式：

主键索引区:PI(关联保存的时数据的地址)按主键查询,

普通索引区:si(关联的id的地址,然后再到达上面的地址)。所以按主键查询,速度最快

B+tree性质：

1.）n棵子tree的节点包含n个关键字，不用来保存数据而是保存数据的索引。

2.）所有的叶子结点中包含了全部关键字的信息，及指向含这些关键字记录的指针，且叶子结点本身依关键字的大小自小而大顺序链接。

3.）所有的非终端结点可以看成是索引部分，结点中仅含其子树中的最大（或最小）关键字。

4.）B+ 树中，数据对象的插入和删除仅在叶节点上进行。

5.）B+树有2个头指针，一个是树的根节点，一个是最小关键码的叶节点。

### 哈希索引

类似于数据结构中简单实现的HASH表（散列表）一样，当我们在mysql中用哈希索引时，主要就是通过Hash算法（常见的Hash算法有直接定址法、平方取中法、折叠法、除数取余法、随机数法），将数据库字段数据转换成定长的Hash值，与这条数据的行指针一并存入Hash表的对应位置；如果发生Hash碰撞（两个不同关键字的Hash值相同），则在对应Hash键下以链表形式存储。当然这只是简略模拟图

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOC85LzI0LzE2NjBjMGYxNThhNzZmOTQ?x-oss-process=image/format,png)

## 索引的基本原理

索引用来快速地寻找那些具有特定值的记录。如果没有索引，一般来说执行查询时遍历整张表。

索引的原理很简单，就是把无序的数据变成有序的查询

1. 把创建了索引的列的内容进行排序
2. 对排序结果生成倒排表
3. 在倒排表内容上拼上数据地址链
4. 在查询的时候，先拿到倒排表内容，再取出数据地址链，从而拿到具体数据

## 索引算法有哪些

索引算法有 BTree算法和Hash算法

### BTree算法

BTree是最常用的mysql数据库索引算法，也是mysql默认的算法。因为它不仅可以被用在=,>,>=,<,<=和between这些比较操作符上，而且还可以用于like操作符，只要它的查询条件是一个不以通配符开头的常量

### Hash算法

Hash Hash索引只能用于对等比较，例如=,<=>（相当于=）操作符。由于是一次定位数据，不像BTree索引需要从根节点到枝节点，最后才能访问到页节点这样多次IO访问，所以检索效率远高于BTree索引

## 使用索引查询一定能提高查询的性能吗

通常，通过索引查询数据比全表扫描要快。但是我们也必须注意到它的代价。

- 索引需要空间来存储，也需要定期维护， 每当有记录在表中增减或索引列被修改时，索引本身也会被修改。 这意味着每条记录的INSERT，DELETE，UPDATE将为此多付出4，5 次的磁盘I/O。 因为索引需要额外的存储空间和处理，那些不必要的索引反而会使查询反应时间变慢。使用索引查询不一定能提高查询性能，索引范围查询(INDEX RANGE SCAN)适用于两种情况:
- 基于一个范围的检索，一般查询返回结果集小于表中记录数的30%
- 基于非唯一性索引的检索

## 百万级别或以上的数据如何删除

关于索引：由于索引需要额外的维护成本，因为索引文件是单独存在的文件,所以当我们对数据的增加,修改,删除,都会产生额外的对索引文件的操作,这些操作需要消耗额外的IO,会降低增/改/删的执行效率。所以，在我们删除数据库百万级别数据的时候，查询MySQL官方手册得知删除数据的速度和创建的索引数量是成正比的。

1. 所以我们想要删除百万数据的时候可以先删除索引（此时大概耗时三分多钟）
2. 然后删除其中无用数据（此过程需要不到两分钟）
3. 删除完成后重新创建索引(此时数据较少了)创建索引也非常快，约十分钟左右。
4. 与之前的直接删除绝对是要快速很多，更别说万一删除中断,一切删除会回滚。那更是坑了。

## 前缀索引

语法：`index(field(10))`，使用字段值的前10个字符建立索引，默认是使用字段的全部内容建立索引。

前提：前缀的标识度高。比如密码就适合建立前缀索引，因为密码几乎各不相同。

实操的难度：在于前缀截取的长度。

我们可以利用`select count(*)/count(distinct left(password,prefixLen));`，通过从调整`prefixLen`的值（从1自增）查看不同前缀长度的一个平均匹配度，接近1时就可以了（表示一个密码的前`prefixLen`个字符几乎能确定唯一一条记录）

## B树和B+树的区别

在B树中，你可以将键和值存放在内部节点和叶子节点；但在B+树中，内部节点都是键，没有值，叶子节点同时存放键和值。

B+树的叶子节点有一条链相连，而B树的叶子节点各自独立。

## 使用B树的好处

B树可以在内部节点同时存储键和值，因此，把频繁访问的数据放在靠近根节点的地方将会大大提高热点数据的查询效率。这种特性使得B树在特定数据重复多次查询的场景中更加高效。

## 使用B+树的好处

由于B+树的内部节点只存放键，不存放值，因此，一次读取，可以在内存页中获取更多的键，有利于更快地缩小查找范围。 B+树的叶节点由一条链相连，因此，当需要进行一次全数据遍历的时候，B+树只需要使用O(logN)时间找到最小的一个节点，然后通过链进行O(N)的顺序遍历即可。而B树则需要对树的每一层进行遍历，这会需要更多的内存置换次数，因此也就需要花费更多的时间

## Hash索引和B+树所有有什么区别或者说优劣呢

首先要知道Hash索引和B+树索引的底层实现原理：

hash索引底层就是hash表，进行查找时，调用一次hash函数就可以获取到相应的键值，之后进行回表查询获得实际数据。B+树底层实现是多路平衡查找树。对于每一次的查询都是从根节点出发，查找到叶子节点方可以获得所查键值，然后根据查询判断是否需要回表查询数据。

那么可以看出他们有以下的不同：

* hash索引进行等值查询更快(一般情况下)，但是却无法进行范围查询。

因为在hash索引中经过hash函数建立索引之后，索引的顺序与原顺序无法保持一致，不能支持范围查询。而B+树的的所有节点皆遵循(左节点小于父节点，右节点大于父节点，多叉树也类似)，天然支持范围。

- hash索引不支持使用索引进行排序，原理同上。
- hash索引不支持模糊查询以及多列索引的最左前缀匹配。原理也是因为hash函数的不可预测。AAAA和AAAAB的索引没有相关性。
- hash索引任何时候都避免不了回表查询数据，而B+树在符合某些条件(聚簇索引，覆盖索引等)的时候可以只通过索引完成查询。
- hash索引虽然在等值查询上较快，但是不稳定。性能不可预测，当某个键值存在大量重复的时候，发生hash碰撞，此时效率可能极差。而B+树的查询效率比较稳定，对于所有的查询都是从根节点到叶子节点，且树的高度较低。

因此，在大多数情况下，直接选择B+树索引可以获得稳定且较好的查询速度。而不需要使用hash索引。

## 数据库为什么使用B+树而不是B树

B树只适合随机检索，而B+树同时支持随机检索和顺序检索；

- B树只适合随机检索，而B+树同时支持随机检索和顺序检索；
- B+树空间利用率更高，可减少I/O次数，磁盘读写代价更低。一般来说，索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式存储的磁盘上。这样的话，索引查找过程中就要产生磁盘I/O消耗。B+树的内部结点并没有指向关键字具体信息的指针，只是作为索引使用，其内部结点比B树小，盘块能容纳的结点中关键字数量更多，一次性读入内存中可以查找的关键字也就越多，相对的，IO读写次数也就降低了。而IO读写次数是影响索引检索效率的最大因素；
- B+树的查询效率更加稳定。B树搜索有可能会在非叶子结点结束，越靠近根节点的记录查找时间越短，只要找到关键字即可确定记录的存在，其性能等价于在关键字全集内做一次二分查找。而在B+树中，顺序检索比较明显，随机检索时，任何关键字的查找都必须走一条从根节点到叶节点的路，所有关键字的查找路径长度相同，导致每一个关键字的查询效率相当。
- B-树在提高了磁盘IO性能的同时并没有解决元素遍历的效率低下的问题。B+树的叶子节点使用指针顺序连接在一起，只要遍历叶子节点就可以实现整棵树的遍历。而且在数据库中基于范围的查询是非常频繁的，而B树不支持这样的操作。
- 增删文件（节点）时，效率更高。因为B+树的叶子节点包含所有关键字，并以有序的链表结构存储，这样可很好提高增删效率。

## B+树在满足聚簇索引和覆盖索引的时候不需要回表查询数据，

在B+树的索引中，叶子节点可能存储了当前的key值，也可能存储了当前的key值以及整行的数据，这就是聚簇索引和非聚簇索引。 在InnoDB中，只有主键索引是聚簇索引，如果没有主键，则挑选一个唯一键建立聚簇索引。如果没有唯一键，则隐式的生成一个键来建立聚簇索引。

当查询使用聚簇索引时，在对应的叶子节点，可以获取到整行数据，因此不用再次进行回表查询。

## 什么是聚簇索引？何时使用聚簇索引与非聚簇索引

- 聚簇索引：将数据存储与索引放到了一块，找到索引也就找到了数据
- 非聚簇索引：将数据存储于索引分开结构，索引结构的叶子节点指向了数据的对应行，myisam通过key_buffer把索引先缓存到内存中，当需要访问数据时（通过索引访问数据），在内存中直接搜索索引，然后通过索引找到磁盘相应数据，这也就是为什么索引不在key buffer命中时，速度慢的原因

澄清一个概念：innodb中，在聚簇索引之上创建的索引称之为辅助索引，辅助索引访问数据总是需要二次查找，非聚簇索引都是辅助索引，像复合索引、前缀索引、唯一索引，辅助索引叶子节点存储的不再是行的物理位置，而是主键值

何时使用聚簇索引与非聚簇索引

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMDE1NDQ5OS1kNTNhNWNlOWNlY2YyMmYzLnBuZw?x-oss-process=image/format,png)

## 非聚簇索引一定会回表查询吗

不一定，这涉及到查询语句所要求的字段是否全部命中了索引，如果全部命中了索引，那么就不必再进行回表查询。

举个简单的例子，假设我们在员工表的年龄上建立了索引，那么当进行`select age from employee where age < 20`的查询时，在索引的叶子节点上，已经包含了age信息，不会再次进行回表查询。

# 事务

## 事物的四大特性(ACID)

1. **原子性：** 事务是最小的执行单位，不允许分割。事务的原子性确保动作要么全部完成，要么完全不起作用；
2. **一致性：** 执行事务前后，数据保持一致，多个事务对同一个数据读取的结果是相同的；
3. **隔离性：** 并发访问数据库时，一个用户的事务不被其他事务所干扰，各并发事务之间数据库是独立的；
4. **持久性：** 一个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发生故障也不应该对其有任何影响。

## 脏读？幻读？不可重复读？

- 脏读(Drity Read)：某个事务已更新一份数据，另一个事务在此时读取了同一份数据，由于某些原因，前一个RollBack了操作，则后一个事务所读取的数据就会是不正确的。
- 不可重复读(Non-repeatable read):在一个事务的两次查询之中数据不一致，这可能是两次查询过程中间插入了一个事务更新的原有的数据。
- 幻读(Phantom Read):在一个事务的两次查询中数据笔数不一致，例如有一个事务查询了几列(Row)数据，而另一个事务却在此时插入了新的几列数据，先前的事务在接下来的查询中，就会发现有几列数据是它先前所没有的。

## 事务的隔离级别

| 隔离级别         | 脏读 | 不可重复读 | 幻影读 |
| ---------------- | ---- | ---------- | ------ |
| READ-UNCOMMITTED | √    | √          | √      |
| READ-COMMITTED   | ×    | √          | √      |
| REPEATABLE-READ  | ×    | ×          | √      |
| SERIALIZABLE     | ×    | ×          | ×      |

**SQL 标准定义了四个隔离级别：**

- **READ-UNCOMMITTED(读取未提交)：** 最低的隔离级别，允许读取尚未提交的数据变更，**可能会导致脏读、幻读或不可重复读**。
- **READ-COMMITTED(读取已提交)：** 允许读取并发事务已经提交的数据，**可以阻止脏读，但是幻读或不可重复读仍有可能发生**。
- **REPEATABLE-READ(可重复读)：** 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，**可以阻止脏读和不可重复读，但幻读仍有可能发生**。
- **SERIALIZABLE(可串行化)：** 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。

这里需要注意的是：Mysql 默认采用的 REPEATABLE_READ隔离级别 Oracle 默认采用的 READ_COMMITTED隔离级别

事务隔离机制的实现基于锁机制和并发调度。其中并发调度使用的是MVVC（多版本并发控制），通过保存修改的旧版本信息来支持并发一致性读和回滚等特性。

因为隔离级别越低，事务请求的锁越少，所以大部分数据库系统的隔离级别都是**READ-COMMITTED(读取提交内容):**，但是你要知道的是InnoDB 存储引擎默认使用 **REPEATABLE-READ（可重读）**并不会有任何性能损失。

InnoDB 存储引擎在 **分布式事务** 的情况下一般会用到**SERIALIZABLE(可串行化)**隔离级别。

# 日志

## 日志文件(顺序IO)

因为日志文件是新增的,不会存在浪费空间

随机IO,不会浪费空间,所以适合做增删改查

## 二进制日志（bin log）

```sql
mysql> show variables like 'log_%';
+----------------------------------------+---------------------+
| Variable_name                          | Value               |
+----------------------------------------+---------------------+
| log_bin                                | OFF                 | (错误日志关闭了,默认是关闭)
| log_bin_basename                       |                     |
| log_bin_index                          |                     |
| log_bin_trust_function_creators        | OFF                 |
| log_bin_use_v1_row_events              | OFF                 |
| log_error                              | /var/log/mysqld.log |
| log_output                             | FILE                |
| log_queries_not_using_indexes          | OFF                 |
| log_slave_updates                      | OFF                 |
| log_slow_admin_statements              | OFF                 |
| log_slow_slave_statements              | OFF                 |
| log_throttle_queries_not_using_indexes | 0                   |
| log_warnings                           | 1                   |
+----------------------------------------+---------------------+
13 rows in set (0.01 sec)
```

需要配置my.cnf开启

```properties
log-bin=mysql-bin
```

```sql
mysql> show variables like 'log_%';
+----------------------------------------+--------------------------------+
| Variable_name                          | Value                          |
+----------------------------------------+--------------------------------+
| log_bin                                | ON                             |
| log_bin_basename                       | /var/lib/mysql/mysql-bin       |
| log_bin_index                          | /var/lib/mysql/mysql-bin.index |
| log_bin_trust_function_creators        | OFF                            |
| log_bin_use_v1_row_events              | OFF                            |
| log_error                              | /var/log/mysqld.log            |
| log_output                             | FILE                           |
| log_queries_not_using_indexes          | OFF                            |
| log_slave_updates                      | OFF                            |
| log_slow_admin_statements              | OFF                            |
| log_slow_slave_statements              | OFF                            |
| log_throttle_queries_not_using_indexes | 0                              |
| log_warnings                           | 1                              |
+----------------------------------------+--------------------------------+
13 rows in set (0.00 sec)
```

其中mysql-bin是binlog日志文件的basename，binlog日志文件的完整名称： mysql-bin-000001.log

binlog记录了数据库所有的ddl语句和dml语句，但不包括select语句内容，语句以事件的形式保存，描述了数据的
变更顺序，binlog还包括了每个更新语句的执行时间信息。如果是DDL语句，则直接记录到binlog日志，而DML语
句，必须通过事务提交才能记录到binlog日志中。

binlog主要用于实现mysql主从复制、数据备份、数据恢复。

## 通用查询日志（general query log）

默认情况下通用查询日志是关闭的。

由于通用查询日志会记录用户的所有操作，其中还包含增删查改等信息，在并发操作大的环境下会产生大量的信息从
而导致不必要的磁盘IO，会影响mysql的性能的。**如若不是为了调试数据库的目的建议生产环境不要开启查询日志**。

```sql
mysql> show global variables like 'general_log';
```

开启方式：

```properties
#启动开关
general_log={ON|OFF}
#日志文件变量，而general_log_file如果没有指定，默认名是host_name.log
general_log_file=/PATH/TO/file 
#记录类型
log_output={TABLE|FILE|NONE}
注： 文件位置在mysql下，否则没有权限创建文件
```

## 慢日志查询（slow query log）

默认是关闭的。
需要通过以下设置进行开启：

```properties
#开启慢查询日志
slow_query_log=ON
#慢查询的阈值
long_query_time=3
#日志记录文件如果没有给出file_name值， 默认为主机名，后缀为-slow.log。如果给出了文件名，但不是绝对路径
名，文件则写入数据目录。
slow_query_log_file=file_name
```

记录执行时间超过long_query_time秒的所有查询，便于收集查询时间比较长的SQL语句

## MySQL的binlog格式

- statement模式下，每一条会修改数据的sql都会记录在binlog中。不需要记录每一行的变化，减少了binlog日志量，节约了IO，提高性能。由于sql的执行是有上下文的，因此在保存的时候需要保存相关的信息，同时还有一些使用了函数之类的语句无法被记录复制。
- row级别下，不记录sql语句上下文相关信息，仅保存哪条记录被修改。记录单元为每一行的改动，基本是可以全部记下来但是由于很多操作，会导致大量行的改动(比如alter table)，因此这种模式的文件保存的信息太多，日志量太大。
- mixed，一种折中的方案，普通操作使用statement记录，当无法使用statement的时候使用row。

## 关心过业务系统里面的sql耗时吗？统计过慢查询吗？对慢查询都怎么优化过？

在业务系统中，除了使用主键进行的查询，其他的我都会在测试库上测试其耗时，慢查询的统计主要由运维在做，会定期将业务中的慢查询反馈给我们。

慢查询的优化首先要搞明白慢的原因是什么？ 是查询条件没有命中索引？是load了不需要的数据列？还是数据量太大？

所以优化也是针对这三个方向来的，

- 首先分析语句，看看是否load了额外的数据，可能是查询了多余的行并且抛弃掉了，可能是加载了许多结果中并不需要的列，对语句进行分析以及重写。
- 分析语句的执行计划，然后获得其使用索引的情况，之后修改语句或者修改索引，使得语句可以尽可能的命中索引。
- 如果对语句的优化已经无法进行，可以考虑表中的数据量是否太大，如果是的话可以进行横向或者纵向的分表。

锁表

InnoDB行锁是通过给索引上的索引项加锁来实现的，InnoDB这种行锁实现特点意味着：只有通过索引条件检索数据，InnoDB才使用行级锁，否则，InnoDB将使用表锁！

数据库被创建成数据目录中的一个目录

* 通过仓库默认安装的,数据目录是/var/lib/mysql
* 通过二进制文件安装的,数据目录是/usr/local/mysql/data
* `show variables like 'datadir';`可以查看数据目录

3.创建json字段,Json类型还包含一些函数用法

```sql
create table emp_details(
    emp_no int primary key,
    details json
);

insert into emp_details(emp_no,details)
values ('1','{"location":"IN","phone":"+1180000000","email":"abc@ex.com","address":{"linel":"abc","line2":"xyz","city":"guangzhou","pin":"56103"}}
');

mysql> select emp_no ,details->'$.address.pin' from emp_details;
+--------+--------------------------+
| emp_no | details->'$.address.pin' |
+--------+--------------------------+
|      1 | "56103"                  |
+--------+--------------------------+
1 row in set (0.02 sec)

mysql> select emp_no ,details->>'$.address.pin' from emp_details;
+--------+---------------------------+
| emp_no | details->>'$.address.pin' |
+--------+---------------------------+
|      1 | 56103                     |
+--------+---------------------------+
1 row in set (0.02 sec)

```

4.事务回滚,设立保存点,比方说我想给两个用户转账,第一个用户转成功了,第二个失败了,但是又不是全部回滚,就可以在一个用户转账成功之后设置保存点,

```sql
mysql> begin;
mysql> savepoint transfer_to_b;
mysql> rollback to transfer_to_b;
mysql> commit;
```

4.mysql锁

共享锁:当一个表被锁定为Read时,多个会话可以从表中读取数据而不需要获取锁,因此,多个会话在同一个表上获得锁,所以被称为共享锁,当READ锁被保持时,没有会话可以将数据写入表中,包括持有该锁的会话,如果有任何写入操作,该操作将处于等待装袋,知道READ锁被释放

排他锁:当一个表被锁定为WRITE时,除了持有该锁的会话之外,其他任何会话都不能读取或向表中写入数据,除非现有锁被释放,否则其他任何会话都不能获得任何锁,这就是称为排他锁,如果有任何读取/写入尝试,改操作将处于等待状态,知道WRITE锁被释放

InnoDB行级锁只是通过索引条件检索数据,才能使用行级锁,否则使用的是表锁

# 存储引擎

## MySQL存储引擎MyISAM与InnoDB区别

MySQL中的数据、索引以及其他对象是如何存储的，是一套文件系统的实现。

常用的存储引擎有以下：

- **Innodb引擎**：Innodb引擎提供了对数据库ACID事务的支持。并且还提供了行级锁和外键的约束。它的设计的目标就是处理大数据容量的数据库系统。
- **MyIASM引擎**(原本Mysql的默认引擎)：不提供事务的支持，也不支持行级锁和外键。
- **MEMORY引擎**：所有的数据都在内存中，数据的处理速度快，但是安全性不高。

|                                                              | MyISAM                                                       | Innodb                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 存储结构                                                     | 每张表被存放在三个文件：frm-表格定义、MYD(MYData)-数据文件、MYI(MYIndex)-索引文件 | 所有的表都保存在同一个数据文件中（也可能是多个文件，或者是独立的表空间文件），InnoDB表的大小只受限于操作系统文件的大小，一般为2GB |
| 存储空间                                                     | MyISAM可被压缩，存储空间较小                                 | InnoDB的表需要更多的内存和存储，它会在主内存中建立其专用的缓冲池用于高速缓冲数据和索引 |
| 可移植性、备份及恢复                                         | 由于MyISAM的数据是以文件的形式存储，所以在跨平台的数据转移中会很方便。在备份和恢复时可单独针对某个表进行操作 | 免费的方案可以是拷贝数据文件、备份 binlog，或者用 mysqldump，在数据量达到几十G的时候就相对痛苦了 |
| 文件格式                                                     | 数据和索引是分别存储的，数据`.MYD`，索引`.MYI`               | 数据和索引是集中存储的，`.ibd`                               |
| 记录存储顺序                                                 | 按记录插入顺序保存                                           | 按主键大小有序插入                                           |
| 外键                                                         | 不支持                                                       | 支持                                                         |
| 事务                                                         | 不支持                                                       | 支持                                                         |
| 锁支持（锁是避免资源争用的一个机制，MySQL锁对用户几乎是透明的） | 表级锁定                                                     | 行级锁定、表级锁定，锁定力度小并发能力高                     |
| SELECT                                                       | MyISAM更优                                                   |                                                              |
| INSERT、UPDATE、DELETE                                       |                                                              | InnoDB更优                                                   |
| select count(*)                                              | myisam更快，因为myisam内部维护了一个计数器，可以直接调取。   |                                                              |
| 索引的实现方式                                               | B+树索引，myisam 是堆表                                      | B+树索引，Innodb 是索引组织表                                |
| 哈希索引                                                     | 不支持                                                       | 支持                                                         |
| 全文索引                                                     | 支持                                                         | 不支持                                                       |
| 索引                                                         | 非聚簇索引                                                   | 聚簇索引                                                     |

## InnoDB引擎的4大特性

- 插入缓冲（insert buffer)
- 二次写(double write)
- 自适应哈希索引(ahi)
- 预读(read ahead)

# 锁

## 对MySQL的锁了解吗

当数据库有并发事务的时候，可能会产生数据的不一致，这时候需要一些机制来保证访问的次序，锁机制就是这样的一个机制。

就像酒店的房间，如果大家随意进出，就会出现多人抢夺同一个房间的情况，而在房间上装上锁，申请到钥匙的人才可以入住并且将房间锁起来，其他人只有等他使用完毕才可以再次使用。

## 隔离级别与锁的关系

在Read Uncommitted级别下，读取数据不需要加共享锁，这样就不会跟被修改的数据上的排他锁冲突

在Read Committed级别下，读操作需要加共享锁，但是在语句执行完以后释放共享锁；

在Repeatable Read级别下，读操作需要加共享锁，但是在事务提交之前并不释放共享锁，也就是必须等待事务执行完毕以后才释放共享锁。

SERIALIZABLE 是限制性最强的隔离级别，因为该级别**锁定整个范围的键**，并一直持有锁，直到事务完成。

## 按照锁的粒度分数据库锁有哪些？锁机制与InnoDB锁算法

在关系型数据库中，可以**按照锁的粒度把数据库锁分**为行级锁(INNODB引擎)、表级锁(MYISAM引擎)和页级锁(BDB引擎 )。

**MyISAM和InnoDB存储引擎使用的锁：**

- MyISAM采用表级锁(table-level locking)。
- InnoDB支持行级锁(row-level locking)和表级锁，默认为行级锁

行级锁，表级锁和页级锁对比

**行级锁** 行级锁是Mysql中锁定粒度最细的一种锁，表示只针对当前操作的行进行加锁。行级锁能大大减少数据库操作的冲突。其加锁粒度最小，但加锁的开销也最大。行级锁分为`共享锁 `和 `排他锁`。

特点：开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高。

**表级锁** 表级锁是MySQL中锁定粒度最大的一种锁，表示对当前操作的整张表加锁，它实现简单，资源消耗较少，被大部分MySQL引擎支持。最常使用的MYISAM与INNODB都支持表级锁定。表级锁定分为表共享读锁（共享锁）与表独占写锁（排他锁）。

特点：开销小，加锁快；不会出现死锁；锁定粒度大，发出锁冲突的概率最高，并发度最低。

**页级锁** 页级锁是MySQL中锁定粒度介于行级锁和表级锁中间的一种锁。表级锁速度快，但冲突多，行级冲突少，但速度慢。所以取了折衷的页级，一次锁定相邻的一组记录。

特点：开销和加锁时间界于表锁和行锁之间；会出现死锁；锁定粒度界于表锁和行锁之间，并发度一般

## 从锁的类别上分MySQL都有哪些锁呢？像上面那样子进行锁定岂不是有点阻碍并发效率了

**从锁的类别上来讲**，有共享锁和排他锁。

共享锁: 又叫做读锁。 当用户要进行数据的读取时，对数据加上共享锁。共享锁可以同时加上多个。

排他锁: 又叫做写锁。 当用户要进行数据的写入时，对数据加上排他锁。排他锁只可以加一个，他和其他的排他锁，共享锁都相斥。

用上面的例子来说就是用户的行为有两种，一种是来看房，多个用户一起看房是可以接受的。 一种是真正的入住一晚，在这期间，无论是想入住的还是想看房的都不可以。

锁的粒度取决于具体的存储引擎，InnoDB实现了行级锁，页级锁，表级锁。

他们的加锁开销从大到小，并发能力也是从大到小。

## MySQL中InnoDB引擎的行锁是怎么实现的？

答：InnoDB是基于索引来完成行锁

例: select * from tab_with_index where id = 1 for update;

for update 可以根据条件来完成行锁锁定，并且 id 是有索引键的列，如果 id 不是索引键那么InnoDB将完成表锁，并发将无从谈起

### InnoDB存储引擎的锁的算法有三种

- Record lock：单个行记录上的锁
- Gap lock：间隙锁，锁定一个范围，不包括记录本身
- Next-key lock：record+gap 锁定一个范围，包含记录本身

**相关知识点：**

1. innodb对于行的查询使用next-key lock
2. Next-locking keying为了解决Phantom Problem幻读问题
3. 当查询的索引含有唯一属性时，将next-key lock降级为record key
4. Gap锁设计的目的是为了阻止多个事务将记录插入到同一范围内，而这会导致幻读问题的产生
5. 有两种方式显式关闭gap锁：（除了外键约束和唯一性检查外，其余情况仅使用record lock） A. 将事务隔离级别设置为RC B. 将参数innodb_locks_unsafe_for_binlog设置为1

### 什么是死锁？怎么解决？

死锁是指两个或多个事务在同一资源上相互占用，并请求锁定对方的资源，从而导致恶性循环的现象。

常见的解决死锁的方法

1、如果不同程序会并发存取多个表，尽量约定以相同的顺序访问表，可以大大降低死锁机会。

2、在同一个事务中，尽可能做到一次锁定所需要的所有资源，减少死锁产生概率；

3、对于非常容易产生死锁的业务部分，可以尝试使用升级锁定颗粒度，通过表级锁定来减少死锁产生的概率；

如果业务处理不好可以用分布式事务锁或者使用乐观锁

### 数据库的乐观锁和悲观锁是什么？怎么实现的？

数据库管理系统（DBMS）中的并发控制的任务是确保在多个事务同时存取数据库中同一数据时不破坏事务的隔离性和统一性以及数据库的统一性。乐观并发控制（乐观锁）和悲观并发控制（悲观锁）是并发控制主要采用的技术手段。

**悲观锁**：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作。在查询完数据的时候就把事务锁起来，直到提交事务。实现方式：使用数据库中的锁机制

**乐观锁**：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。在修改数据的时候把事务锁起来，通过version的方式来进行锁定。实现方式：乐一般会使用版本号机制或CAS算法实现。

**两种锁的使用场景**

从上面对两种锁的介绍，我们知道两种锁各有优缺点，不可认为一种好于另一种，像**乐观锁适用于写比较少的情况下（多读场景）**，即冲突真的很少发生的时候，这样可以省去了锁的开销，加大了系统的整个吞吐量。

但如果是多写的情况，一般会经常产生冲突，这就会导致上层应用会不断的进行retry，这样反倒是降低了性能，所以**一般多写的场景下用悲观锁就比较合适。**

# ExPlan

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200310171131582.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoaW5rV29u,size_16,color_FFFFFF,t_70)

## ID

执行计划包含的信息 **id** 有一组数字组成。表示一个查询中各个子查询的执行顺序;

- id相同执行顺序由上至下。
- id不同，id值越大优先级越高，越先被执行。
- id为null时表示一个结果集，不需要使用它查询，常出现在包含union等查询语句中。

## **select_type** 

| id   | select_type  | description                               |
| ---- | ------------ | ----------------------------------------- |
| 1    | SIMPLE       | 不包含任何子查询或union等查询             |
| 2    | PRIMARY      | 包含子查询最外层查询就显示为 PRIMARY      |
| 3    | SUBQUERY     | 在select或 where字句中包含的查询          |
| 4    | DERIVED      | from字句中包含的查询                      |
| 5    | UNION        | 出现在union后的查询语句中                 |
| 6    | UNION RESULT | 从UNION中获取结果集，例如上文的第三个例子 |

## table

**table** 查询的数据表，当从衍生表中查数据时会显示 x 表示对应的执行计划id **partitions** 表分区、表创建的时候可以指定通过那个列进行表分区

## type

**type**(非常重要，可以看到有没有走索引) 访问类型

- ALL 扫描全表数据
- index 遍历索引
- range 索引范围查找
- index_subquery 在子查询中使用 ref
- unique_subquery 在子查询中使用 eq_ref
- ref_or_null 对Null进行索引的优化的 ref
- fulltext 使用全文索引
- ref 使用非唯一索引查找数据
- eq_ref 在join查询中使用PRIMARY KEYorUNIQUE NOT NULL索引关联。

## possible_keys

可能使用的索引，注意不一定会使用。查询涉及到的字段上若存在索引，则该索引将被列出来。当该列为 NULL时就要考虑当前的SQL是否需要优化了。

## Key

显示MySQL在查询中实际使用的索引，若没有使用索引，显示为NULL。

**TIPS**:查询中若使用了覆盖索引(覆盖索引：索引的数据覆盖了需要查询的所有数据)，则该索引仅出现在key列表中

## **key_length** 

索引长度

## **ref** 

表示上述表的连接匹配条件，即哪些列或常量被用于查找索引列上的值

## rows

返回估算的结果集数目，并不是一个准确的值。

## **extra**

1. Using index 使用覆盖索引
2. Using where 使用了用where子句来过滤结果集
3. Using filesort 使用文件排序，使用非索引列进行排序时出现，非常消耗性能，尽量优化。
4. Using temporary 使用了临时表 sql优化的目标可以参考阿里开发手册

【推荐】SQL性能优化的目标：至少要达到 range 级别，要求是ref级别，如果可以是consts最好。 
说明： 
1） consts 单表中最多只有一个匹配行（主键或者唯一索引），在优化阶段即可读取到数据。 
2） ref 指的是使用普通的索引（normal index）。 
3） range 对索引进行范围检索。 
反例：explain表的结果，type=index，索引物理文件全扫描，速度非常慢，这个index级别比较range还低，与全表扫描是小巫见大巫。

# SQL的生命周期

1. 应用服务器与数据库服务器建立一个连接
2. 数据库进程拿到请求sql
3. 解析并生成执行计划，执行
4. 读取数据到内存并进行逻辑处理
5. 通过步骤一的连接，发送结果到客户端
6. 关掉连接，释放资源

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200310170936478.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoaW5rV29u,size_16,color_FFFFFF,t_70)

![img](https://raw.githubusercontent.com/YangAnLin/images/master/20200824203057.webp)

## 查询流程

Server层

1. 连接器： 身份认证和权限相关(登录 MySQL 的时候)。

2. 查询缓存: 执行查询语句的时候，会先查询缓存（MySQL 8.0 版本后移除，因为这个功能不太实用）。

3. 分析器: 没有命中缓存的话，SQL 语句就会经过分析器，分析器说白了就是要先看你的 SQL 语句要干嘛，再检查你的 SQL 语句语法是否正确。

4. 优化器： 按照 MySQL 认为最优的方案去执行。
5. 执行器: 执行语句，然后从存储引擎返回数据。

存储引擎

## 连接器

你会先连接到这个数据库上，这时候接待你的就是连接器

* 长连接是指连接成功后，如果客户端持续有请求，则一直使用同一个连接。,尽量减少建立连接的动作，也就是尽量使用长连接。
* 短连接则是指每次执行完很少的几次查询就断开连接，下次查询再重新建立一个。

全部使用长连接后，你可能会发现，有些时候 MySQL 占用内存涨得特别快，这是因为 MySQL 在执行过程中临时使用的内存是管理在连接对象里面的。这些资源会在连接断开的时候才释放。所以如果长连接累积下来，可能导致内存占用太大，被系统强行杀掉（OOM），从现象看就是 MySQL 异常重启了。

解决方法:

1. 定期断开长连接。使用一段时间，或者程序里面判断执行过一个占用内存的大查询后，断开连接，之后要查询再重连。
2. 如果你用的是 MySQL 5.7 或更新版本，可以在每次执行一个比较大的操作后，通过执行 mysql_reset_connection 来重新初始化连接资源。这个过程不需要重连和重新做权限验证，但是会将连接恢复到刚刚创建完时的状态

## 分析器

### 词法分析

MySQL 从你输入的"select"这个关键字识别出来，这是一个查询语句。它也要把字符串“T”识别成“表名 T”，把字符串“ID”识别成“列 ID”。

### 语法分析

做完了这些识别以后，就要做“语法分析”。根据词法分析的结果，语法分析器会根据语法规则，判断你输入的这个 SQL 语句是否满足 MySQL 语法。

一般语法错误会提示第一个出现错误的位置，所以你要关注的是紧接“use near”的内容

## 优化器

优化器是在表里面有多个索引的时候，决定使用哪个索引；

或者在一个语句有多表关联（join）的时候，决定各个表的连接顺序。

## 执行器

先查询有没有权限

```sql
ERROR 1142 (42000): SELECT command denied to user 'b'@'localhost' for table 'T'
```

# Sql优化

## 大表数据查询，怎么优化

1. 优化shema、sql语句+索引；
2. 第二加缓存，memcached, redis；
3. 主从复制，读写分离；
4. 垂直拆分，根据你模块的耦合度，将一个大的系统分为多个小的系统，也就是分布式系统；
5. 水平切分，针对数据量大的表，这一步最麻烦，最能考验技术水平，要选择一个合理的sharding key, 为了有好的查询效率，表结构也要改动，做一定的冗余，应用也要改，sql中尽量带sharding key，将数据定位到限定的表上去查，而不是扫描全部的表；

## 超大分页怎么处理？

超大的分页一般从两个方向上来解决.

- 数据库层面,这也是我们主要集中关注的(虽然收效没那么大),类似于`select * from table where age > 20 limit 1000000,10`这种查询其实也是有可以优化的余地的. 这条语句需要load1000000数据然后基本上全部丢弃,只取10条当然比较慢. 当时我们可以修改为`select * from table where id in (select id from table where age > 20 limit 1000000,10)`.这样虽然也load了一百万的数据,但是由于索引覆盖,要查询的所有字段都在索引中,所以速度会很快. 同时如果ID连续的好,我们还可以`select * from table where id > 1000000 limit 10`,效率也是不错的,优化的可能性有许多种,但是核心思想都一样,就是减少load的数据.
- 从需求的角度减少这种请求…主要是不做类似的需求(直接跳转到几百万页之后的具体某一页.只允许逐页查看或者按照给定的路线走,这样可预测,可缓存)以及防止ID泄漏且连续被人恶意攻击.

【推荐】利用延迟关联或者子查询优化超多分页场景。 

说明：MySQL并不是跳过offset行，而是取offset+N行，然后返回放弃前offset行，返回N行，那当offset特别大的时候，效率就非常的低下，要么控制返回的总页数，要么对超过特定阈值的页数进行SQL改写。 

正例：先快速定位需要获取的id段，然后再关联： 

SELECT a.* FROM 表1 a, (select id from 表1 where 条件 LIMIT 100000,20 ) b where a.id=b.id

## 为什么要尽量设定一个主键？

主键是数据库确保数据行在整张表唯一性的保障，即使业务上本张表没有主键，也建议添加一个自增长的ID列作为主键。设定了主键之后，在后续的删改查的时候可能更加快速以及确保操作数据范围安全。

## 主键使用自增ID还是UUID？

推荐使用自增ID，不要使用UUID。

因为在InnoDB存储引擎中，主键索引是作为聚簇索引存在的，也就是说，主键索引的B+树叶子节点上存储了主键索引以及全部的数据(按照顺序)，如果主键索引是自增ID，那么只需要不断向后排列即可，如果是UUID，由于到来的ID与原来的大小不确定，会造成非常多的数据插入，数据移动，然后导致产生很多的内存碎片，进而造成插入性能的下降。

总之，在数据量大一些的情况下，用自增主键性能会好一些。

关于主键是聚簇索引，如果没有主键，InnoDB会选择一个唯一键来作为聚簇索引，如果没有唯一键，会生成一个隐式的主键。

### 字段为什么要求定义为not null？

null值会占用更多的字节，且会在程序中造成很多与预期不符的情况。

### 如果要存储用户的密码散列，应该使用什么字段进行存储？

密码散列，盐，用户身份证号等固定长度的字符串应该使用char而不是varchar来存储，这样可以节省空间且提高检索效率。

### 优化查询过程中的数据访问

- 访问数据太多导致查询性能下降
- 确定应用程序是否在检索大量超过需要的数据，可能是太多行或列
- 确认MySQL服务器是否在分析大量不必要的数据行
- 避免犯如下SQL语句错误
- 查询不需要的数据。解决办法：使用limit解决
- 多表关联返回全部列。解决办法：指定列名
- 总是返回全部列。解决办法：避免使用SELECT *
- 重复查询相同的数据。解决办法：可以缓存数据，下次直接读取缓存
- 是否在扫描额外的记录。解决办法：
- 使用explain进行分析，如果发现查询需要扫描大量的数据，但只返回少数的行，可以通过如下技巧去优化：
- 使用索引覆盖扫描，把所有的列都放到索引中，这样存储引擎不需要回表获取对应行就可以返回结果。
- 改变数据库和表的结构，修改数据表范式
- 重写SQL语句，让优化器可以以更优的方式执行查询。

### 优化长难的查询语句

- 一个复杂查询还是多个简单查询
- MySQL内部每秒能扫描内存中上百万行数据，相比之下，响应数据给客户端就要慢得多
- 使用尽可能小的查询是好的，但是有时将一个大的查询分解为多个小的查询是很有必要的。
- 切分查询
- 将一个大的查询分为多个小的相同的查询
- 一次性删除1000万的数据要比一次删除1万，暂停一会的方案更加损耗服务器开销。
- 分解关联查询，让缓存的效率更高。
- 执行单个查询可以减少锁的竞争。
- 在应用层做关联更容易对数据库进行拆分。
- 查询效率会有大幅提升。
- 较少冗余记录的查询。

### 优化特定类型的查询语句

- count(*)会忽略所有的列，直接统计所有列数，不要使用count(列名)
- MyISAM中，没有任何where条件的count(*)非常快。
- 当有where条件时，MyISAM的count统计不一定比其它引擎快。
- 可以使用explain查询近似值，用近似值替代count(*)
- 增加汇总表
- 使用缓存

### 优化关联查询

- 确定ON或者USING子句中是否有索引。
- 确保GROUP BY和ORDER BY只有一个表中的列，这样MySQL才有可能使用索引。

### 优化子查询

- 用关联查询替代
- 优化GROUP BY和DISTINCT
- 这两种查询据可以使用索引来优化，是最有效的优化方法
- 关联查询中，使用标识列分组的效率更高
- 如果不需要ORDER BY，进行GROUP BY时加ORDER BY NULL，MySQL不会再进行文件排序。
- WITH ROLLUP超级聚合，可以挪到应用程序处理

### 优化LIMIT分页

- LIMIT偏移量大的时候，查询效率较低
- 可以记录上次查询的最大ID，下次查询时直接根据该ID来查询

### 优化UNION查询

UNION ALL的效率高于UNION

### 优化WHERE子句

对于此类考题，先说明如何定位低效SQL语句，然后根据SQL语句可能低效的原因做排查，先从索引着手，如果索引没有问题，考虑以上几个方面，数据访问的问题，长难查询句的问题还是一些特定类型优化的问题，逐一回答。

SQL语句优化的一些方法？

1.对查询进行优化，应尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引。

2.应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描

```sql
select id from t where num is null
-- 可以在num上设置默认值0，确保表中num列没有null值，然后这样查询：
select id from t where num=
```

3.应尽量避免在 where 子句中使用!=或<>操作符，否则引擎将放弃使用索引而进行全表扫描。

4.应尽量避免在 where 子句中使用or 来连接条件，否则将导致引擎放弃使用索引而进行全表扫描，如：

```sql
select id from t where num=10 or num=20
-- 可以这样查询：
select id from t where num=10 union all select id from t where num=20
```

5.in 和 not in 也要慎用，否则会导致全表扫描，如：

```sql
select id from t where num in(1,2,3) 
-- 对于连续的数值，能用 between 就不要用 in 了：
select id from t where num between 1 and 3
```

6.下面的查询也将导致全表扫描：select id from t where name like ‘%李%’若要提高效率，可以考虑全文检索。

7.如果在 where 子句中使用参数，也会导致全表扫描。因为SQL只有在运行时才会解析局部变量，但优化程序不能将访问计划的选择推迟到运行时；它必须在编译时进行选择。然 而，如果在编译时建立访问计划，变量的值还是未知的，因而无法作为索引选择的输入项。如下面语句将进行全表扫描：

8.应尽量避免在 where 子句中对字段进行表达式操作，这将导致引擎放弃使用索引而进行全表扫描

9.应尽量避免在where子句中对字段进行函数操作，这将导致引擎放弃使用索引而进行全表扫描。如：

10.不要在 where 子句中的“=”左边进行函数、算术运算或其他表达式运算，否则系统将可能无法正确使用索引

# 数据库优化

### MySQL的复制原理以及流程

主从复制：将主数据库中的DDL和DML操作通过二进制日志（BINLOG）传输到从数据库上，然后将这些日志重新执行（重做）；从而使得从数据库的数据与主数据库保持一致。

**主从复制的作用**

1. 主数据库出现问题，可以切换到从数据库。
2. 可以进行数据库层面的读写分离。
3. 可以在从数据库上进行日常备份。

**MySQL主从复制解决的问题**

- 数据分布：随意开始或停止复制，并在不同地理位置分布数据备份
- 负载均衡：降低单个服务器的压力
- 高可用和故障切换：帮助应用程序避免单点失败
- 升级测试：可以用更高版本的MySQL作为从库

**MySQL主从复制工作原理**

- 在主库上把数据更高记录到二进制日志
- 从库将主库的日志复制到自己的中继日志
- 从库读取中继日志的事件，将其重放到从库数据中

**基本原理流程，3个线程以及之间的关联**

**主**：binlog线程——记录下所有改变了数据库数据的语句，放进master上的binlog中；

**从**：io线程——在使用start slave 之后，负责从master上拉取 binlog 内容，放进自己的relay log中；

**从**：sql执行线程——执行relay log中的语句；

**复制过程**

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOC85LzIxLzE2NWZiNjgzMjIyMDViMmU?x-oss-process=image/format,png)

Binary log：主数据库的二进制日志

Relay log：从服务器的中继日志

第一步：master在每个事务更新数据完成之前，将该操作记录串行地写入到binlog文件中。

第二步：salve开启一个I/O Thread，该线程在master打开一个普通连接，主要工作是binlog dump process。如果读取的进度已经跟上了master，就进入睡眠状态并等待master产生新的事件。I/O线程最终的目的是将这些事件写入到中继日志中。

第三步：SQL Thread会读取中继日志，并顺序执行该日志中的SQL事件，从而与主数据库中的数据保持一致。

### 备份计划，mysqldump以及xtranbackup的实现原理

**(1)备份计划**

视库的大小来定，一般来说 100G 内的库，可以考虑使用 mysqldump 来做，因为 mysqldump更加轻巧灵活，备份时间选在业务低峰期，可以每天进行都进行全量备份(mysqldump 备份出来的文件比较小，压缩之后更小)。

100G 以上的库，可以考虑用 xtranbackup 来做，备份速度明显要比 mysqldump 要快。一般是选择一周一个全备，其余每天进行增量备份，备份时间为业务低峰期。

**(2)备份恢复时间**

物理备份恢复快，逻辑备份恢复慢

这里跟机器，尤其是硬盘的速率有关系，以下列举几个仅供参考

20G的2分钟（mysqldump）

80G的30分钟(mysqldump)

111G的30分钟（mysqldump)

288G的3小时（xtra)

3T的4小时（xtra)

逻辑导入时间一般是备份时间的5倍以上

**(3)备份恢复失败如何处理**

首先在恢复之前就应该做足准备工作，避免恢复的时候出错。比如说备份之后的有效性检查、权限检查、空间检查等。如果万一报错，再根据报错的提示来进行相应的调整。

**(4)mysqldump和xtrabackup实现原理**

mysqldump

mysqldump 属于逻辑备份。加入–single-transaction 选项可以进行一致性备份。后台进程会先设置 session 的事务隔离级别为 RR(SET SESSION TRANSACTION ISOLATION LEVELREPEATABLE READ)，之后显式开启一个事务(START TRANSACTION /*!40100 WITH CONSISTENTSNAPSHOT */)，这样就保证了该事务里读到的数据都是事务事务时候的快照。之后再把表的数据读取出来。如果加上–master-data=1 的话，在刚开始的时候还会加一个数据库的读锁(FLUSH TABLES WITH READ LOCK),等开启事务后，再记录下数据库此时 binlog 的位置(showmaster status)，马上解锁，再读取表的数据。等所有的数据都已经导完，就可以结束事务

Xtrabackup:

xtrabackup 属于物理备份，直接拷贝表空间文件，同时不断扫描产生的 redo 日志并保存下来。最后完成 innodb 的备份后，会做一个 flush engine logs 的操作(老版本在有 bug，在5.6 上不做此操作会丢数据)，确保所有的 redo log 都已经落盘(涉及到事务的两阶段提交

概念，因为 xtrabackup 并不拷贝 binlog，所以必须保证所有的 redo log 都落盘，否则可能会丢最后一组提交事务的数据)。这个时间点就是 innodb 完成备份的时间点，数据文件虽然不是一致性的，但是有这段时间的 redo 就可以让数据文件达到一致性(恢复的时候做的事

情)。然后还需要 flush tables with read lock，把 myisam 等其他引擎的表给备份出来，备份完后解锁。这样就做到了完美的热备。

## 数据表损坏的修复方式有哪些

使用 myisamchk 来修复，具体步骤：

- 1）修复前将mysql服务停止。
- 2）打开命令行方式，然后进入到mysql的/bin目录。
- 3）执行myisamchk –recover 数据库所在路径/*.MYI

使用repair table 或者 OPTIMIZE table命令来修复，REPAIR TABLE table_name 修复表 OPTIMIZE TABLE table_name 优化表 REPAIR TABLE 用于修复被破坏的表。 OPTIMIZE TABLE 用于回收闲置的数据库空间，当表上的数据行被删除时，所占据的磁盘空间并没有立即被回收，使用了OPTIMIZE TABLE命令后这些空间将被回收，并且对磁盘上的数据行进行重排（注意：是磁盘上，而非数据库）

# 文档翻译

## Abstract

This is the MySQL™ Reference Manual. It documents MySQL 5.7 through 5.7.31,

`这是Mysql参考手册,它记录了MySQL 5.7 through 5.7.31`

 as well as NDB Cluster releases based on version 7.5 of NDB through 5.7.30-ndb-7.5.19, respectively. 

It may include documentation of features of MySQL versions that have not yet been released. 

`它肯能还包含了mysql正式版还没有发行的新特性`

For information about which versions have been released, see the MySQL 5.7 Release Notes. 

`有关已发布版本的信息,参考:https://dev.mysql.com/doc/relnotes/mysql/5.7/en/`

**MySQL 5.7 features**. 

This manual describes features that are not included in every edition of MySQL 5.7; 

`这个手册描述,不包含mysql5.7的每个特性`

such features may not be included in the edition of MySQL 5.7 licensed to you. 

`这些特性可能有没有授权给你用的mysql5.7`

If you have any questions about the features included in your edition of MySQL 5.7, refer to your MySQL 5.7 license agreement or contact your Oracle sales representative. 

`如果你有mysql5.7的一些问题,请参考mysql5.7的授权许可,或者联系你的oracle销售代表`

For notes detailing the changes in each release, see the MySQL 5.7 Release Notes. 

`每个版本的变化详情,参考:https://dev.mysql.com/doc/relnotes/mysql/5.7/en/`

For legal information, including licensing information, see the Preface and Legal Notices. 

`法律信息和授权许可信息,参考:https://web.kamihq.com/web/viewer.html?state=%7B%22ids%22%3A%5B%221fY9FEMSbC8qfQBGgrkuThhikYlmcuXyd%22%5D%2C%22action%22%3A%22open%22%2C%22userId%22%3A%22100824614853388086262%22%7D&filename=refman-5.7-en.a4.pdf#page=25&zoom=130,54,769.889`

For help with using MySQL, please visit the MySQL Forums, where you can discuss your issues with other 

MySQL users. 

`有关使用mysql的帮助,请访问mysql论坛,你可以讨论你的问题和其它的mysql使用者`

Document generated on: 2020-05-30 (revision: 66132)

`文档生成与:....`

## 第三节 指南

This chapter provides a tutorial introduction to MySQL by showing how to use the mysql client program to create and use a simple database. 

`第三节提供了介绍怎么通过mysql客户端程序去创建和使用简单的数据库`

mysql (sometimes referred to as the “terminal monitor” or just “monitor”) is an interactive program that enables you to connect to a MySQL server, 

`mysql(有时也叫terminal monitor或者就叫monitor)是一个可交互程序,可以连接你的mysql服务端`

run queries, and view the results. mysql may also be used in batch mode: you place your queries in a file beforehand, then tell mysql to execute the contents of the file. Both ways of using mysql are covered here. 

`运行查询和查看结果,mysql也可以用于批处理模式:你预先把查询放在一个文件里,然后告诉mysql去执行文件里的内容,两种使用方法都在这里`

To see a list of options provided by mysql, invoke it with the --help option: 

`去想看mysql提交的可选列表,进入并使用--help调用它`

```shell
shell> mysql --help 
```

This chapter assumes that mysql is installed on your machine and that a MySQL server is available to which you can connect.

`本章假定,mysql已经安装在你的电脑并且,mysql服务器是连接上并且,可以使用的`

 If this is not true, contact your MySQL administrator. (If you are the administrator, you need to consult the relevant portions of this manual, such as Chapter 5, MySQL Server Administration.) 

`如果这里不对,联系你的mysql管理员(如果你是管理员,你需要查询这个手册的相关部分,比如第五章:mysql服务器管理员)`

This chapter describes the entire process of setting up and using a database. If you are interested only in accessing an existing database, you may want to skip the sections that describe how to create the database and the tables it contains. 

`这章节描述了建立和使用数据库全部过程,如果你只对访问数据库感兴趣,你可以跳过这节关于怎么去创建数据库和数据库表`

Because this chapter is tutorial in nature, many details are necessarily omitted. Consult the relevant sections of the manual for more information on the topics covered here. 

`因为这章节是指南,一些详情必须要跳过,查阅手册的相关章节为了获取更多这里涵盖的主题信息`

### 3.1 Connecting to and Disconnecting from the Server 

`连接和断开服务器`

To connect to the server, you will usually need to provide a MySQL user name when you invoke mysql and, most likely, a password. 

`连接服务器,当你调用mysql,你将经常需要提供mysql用户名,还很有可能提供密码`

If the server runs on a machine other than the one where you login, you will also need to specify a host name. Contact your administrator to find out what connection parameters you should use to connect (that is, what host, user name, and password to use). Once you know the proper parameters, you should be able to connect like this: 

`如果你要登录服务器,而服务器运行在其它机器上,你将需要特别的主机名字,联系你的管理员找到连接需要的参数,连接的时候你需要(设什么主机名,用户名和密码),一旦你知道正确的参数,有应该可以像这样连接`

```shell
shell> mysql -h host -u user -p
Enter password: ******** 
```

host and user represent the host name where your MySQL server is running and the user name of your MySQL account. 

`host和user当前表示的是主机名字,是你运行在mysq服务器的主机名字和mysql账号`

Substitute appropriate values for your setup. The ******** represents your password; enter it when mysql displays the Enter password: prompt. 

`用适当的值替换你设置密码,星号代表你的密码,当mysql显现提示符的时候,密码输入`

If that works, you should see some introductory information followed by a mysql> prompt: 

`如果工作了,在mysql>提示符后面,你应该看一些介绍信息`

```shell
shell> mysql -h host -u user -p 
Enter password: ******** 
Welcome to the MySQL monitor.  Commands end with ; or \g. 
Your MySQL connection id is 25338 to server version: 5.7.31-standard Type 'help;' or '\h' for help. 

Type '\c' to clear the buffer. mysql> 
```

The mysql> prompt tells you that mysql is ready for you to enter SQL statements. If you are logging in on the same machine that MySQL is running on, you can omit the host, and simply use the following: 

`mysql> 提示符告诉你mysql已经准备好你输入sql语句了,如你登录的是跟mysql运行的同一台机器的时候,你可以忽略host,简单的使用如下`

```shell
shell> mysql -u user -p 
```

If, when you attempt to log in, you get an error message such as ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2), it means that the MySQL server daemon (Unix) or service (Windows) is not running. Consult the administrator or see the section of Chapter 2, Installing and Upgrading MySQL that is appropriate to your operating system. 

`如果当你尝试登录,你得到了错误信息,像这样ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2),它代表着mysql服务端守护程序(unix)或者服务(windows)没有运行,咨询管理员或者看第二章,安装和升级mysql在你的适当的操作系统上`

For help with other problems often encountered when trying to log in, see Section B.4.2, “Common Errors When Using MySQL Programs”. 

`当你登录的时候,其它的经常遇见的问题,需要帮助,请看 Section B.4.2, “当你使用mysql时的常见错误”`

Some MySQL installations permit users to connect as the anonymous (unnamed) user to the server running on the local host. If this is the case on your machine, you should be able to connect to that server by invoking mysql without any options: 

`一些mysql安装保证user可以匿名从本地连接运行中的服务器,如果这些案例在你的机器上,你应该可以连接调用,并且不需要使用别的选项`

```shell
shell> mysql 
```

After you have connected successfully, you can disconnect any time by typing QUIT (or \q) at the mysql> prompt: 

`之后你连接成功,你可以断开连接通过输入 quit或者\q`

```shell
mysql> QUIT Bye 
Bye 
```

On Unix, you can also disconnect by pressing Control+D. 

`在unix,你可以输入contro+d断开连接`

Most examples in the following sections assume that you are connected to the server. They indicate this by the mysql> prompt. 

`更多的案例通过假定你已经连接上了服务端,他们都显示mysql>这个提示符`

### 3.2 Entering Queries 

`输入查询`

Make sure that you are connected to the server, as discussed in the previous section. 

`确保你可以连接服务器,如前一节所述,`

Doing so does not in itself select any database to work with, but that is okay. At this point, it is more important to find out a little about how to issue queries than to jump right in creating tables, loading data into them, and retrieving data from them. 

`这样做它本身没有选择数据库做什么,但是是可以做的,在这个时刻,更重要的是找到关于发起查询的标题,而不是跳到正确的创建表,加载数据到数据库和从数据库里检索出数据`

This section describes the basic principles of entering queries, using several queries you can try out to familiarize yourself with how mysql works. 

`这节描述的输入查询的基本原则 可以尝试使用几个查询来熟悉mysql是怎么工作的`

Here is a simple query that asks the server to tell you its version number and the current date. Type it in as shown here following the mysql> prompt and press Enter: 

`这里是简单的查询,请求服务器告诉我它的版本号和当前时间,在mysql>提示符后面输入并按enter键`

```shell
mysql> select version(),current_date;
+-----------+--------------+
| version() | current_date |
+-----------+--------------+
| 8.0.19    | 2020-06-02   |
+-----------+--------------+
1 row in set (0.01 sec)

mysql> 
```

This query illustrates several things about mysql: 
`这里的查询例子关系到mysql的一些事情`

* A query normally consists of an SQL statement followed by a semicolon. (There are some exceptions where a semicolon may be omitted. QUIT, mentioned earlier, is one of them. We'll get to others later.)  
* `一个普通的查询的组成是有sql语句和接在后面的分号(这里有一些例外分号是可以忽略的,前面提及的QUIT就是其中之一,我们之后会获取更多)`
* When you issue a query, mysql sends it to the server for execution and displays the results, then prints another mysql> prompt to indicate that it is ready for another query. 
* `当你发起请求,mysql发送它的数据库和执行和显示结果,然后打印其它的mysql>提示符并等待其它的查询`
* mysql displays query output in tabular form (rows and columns). The first row contains labels for the columns. The rows following are the query results. Normally, column labels are the names of the columns you fetch from database tables. If you're retrieving the value of an expression rather than a table column (as in the example just shown), mysql labels the column using the expression itself. 
* `mysql以表格的形式显示查询出书(行和列),第一行是包含列的标签,行就是查询结果,普遍的,列标签的名字是从数据库查询到的,如果检索的是表达式的值而不是表列(比如刚才的例子),mysql使用表达式本事来标记列`
* mysql shows how many rows were returned and how long the query took to execute, which gives you a rough idea of server performance. These values are imprecise because they represent wall clock time (not CPU or machine time), and because they are affected by factors such as server load and network latency. (For brevity, the “rows in set” line is sometimes not shown in the remaining examples in this chapter.) 
* `mysql当返回的时候显示很多行和执行查询用了多长时间,让你大概了解服务器的性能,这些值是不准确的,它代表的是时间(不是cpu或者机器的时间),因为他们受服务器的加载和网络延迟的等因素影响(简洁起见,rows in set 这行有些时候是不显示的在这节中)`,

Keywords may be entered in any lettercase. The following queries are equivalent: 

`关键字可以是以大小写字体输入都可以,他们都是等价的`

```shell
mysql> SELECT VERSION(), CURRENT_DATE; 
mysql> select version(), current_date; 
mysql> SeLeCt vErSiOn(), current_DATE; 
```

Here is another query. It demonstrates that you can use mysql as a simple calculator: 

`这里还有个查询,你可以使用mysql做一个简单的计算器`

```shell
mysql> SELECT SIN(PI()/4), (4+1)*5; 
+--------------------+---------+
| SIN(PI()/4)        | (4+1)*5 |
+--------------------+---------+
| 0.7071067811865476 |      25 |
+--------------------+---------+
1 row in set (0.02 sec)
```

The queries shown thus far have been relatively short, single-line statements. You can even enter multiple statements on a single line. Just end each one with a semicolon: 

`到目前为止,单行语句相对而言比较短,你可以在单行输入过个语句,在每个结束的的时候加上分号`

```sql
mysql> SELECT VERSION(); SELECT NOW();
+-----------+
| VERSION() |
+-----------+
| 8.0.19    |
+-----------+
1 row in set (0.01 sec)

+---------------------+
| NOW()               |
+---------------------+
| 2020-06-02 14:36:33 |
+---------------------+
1 row in set (0.02 sec)
```

A query need not be given all on a single line, so lengthy queries that require several lines are not a problem. mysql determines where your statement ends by looking for the terminating semicolon, not by looking for the end of the input line. (In other words, mysql accepts free-format input: it collects input lines but does not execute them until it sees the semicolon.) 

`一个查询不需要全部都在一行,所以长的查询需要多行,这不是问题,mysql就决定了当你的语句结束时候通过看命令行的分号,不是看行的结束(换句话说,mysql允许自有格式化输入,它首届了输入行,但是不会执行,直到看到了分号)`

Here is a simple multiple-line statement: 

`这里是简单的多行语句`

```sql
mysql> select 
    -> user()
    -> ,
    -> current_date;
+----------------+--------------+
| user()         | current_date |
+----------------+--------------+
| root@localhost | 2020-06-02   |
+----------------+--------------+
1 row in set (0.02 sec)
```

In this example, notice how the prompt changes from mysql> to -> after you enter the first line of a multiple-line query. This is how mysql indicates that it has not yet seen a complete statement and is waiting for the rest. The prompt is your friend, because it provides valuable feedback. If you use that feedback, you can always be aware of what mysql is waiting for. 

`在这个例子中,注意的是你输入多行中的第一行后,提示符是怎么从mysql>变成->的,这就是mysql标识还没有看到语句完成,它正在等待其它的语句,提示符是你的朋友,因为它提供的有用的反馈,如果你使用这些反馈,你经常可以知道mysql在等待什么`

If you decide you do not want to execute a query that you are in the process of entering, cancel it by typing \c: 

`如果你决定不决定执行查询在你输入的过程中,请按ctrl+c`

```sql
mysql> SELECT
	-> USER()
	-> \c
mysql> 
```

Here, too, notice the prompt. It switches back to mysql> after you type \c, providing feedback to indicate that mysql is ready for a new query. 

`这里,也要注意提示符,当你按了\c之后,又变成的mysql>,提供返回显示mysql准备一个新的查询`

The following table shows each of the prompts you may see and summarizes what they mean about the state that mysql is in. 

`下面的表格显示每个提示符,你可能查看和总结它们在mysql所处的状态的含义`

| Prompt | Meaning                |
| ------ | ---------------------- |
| mysql> | 准备新的查询           |
| ->     | 在多行查询中等待下一行 |
|'>| Waiting for next line, waiting for completion of a string that began with a single quote (') |
|">| Waiting for next line, waiting for completion of a string that began with a double  quote (") |
|`>| Waiting for next line, waiting for completion of an identifier that began with a backtick (`) |
|/*>| Waiting for next line, waiting for completion of a comment that began with /* |

Multiple-line statements commonly occur by accident when you intend to issue a query on a single line, but forget the terminating semicolon. In this case, mysql waits for more input: 

`多行语句`



