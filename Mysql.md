[toc]

# Mysql安装

## 1. 下载 mysql 源安装包

```shell
$ curl -LO http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
```

## 2. 安装 mysql 源

```shell
$ sudo yum localinstall mysql57-community-release-el7-11.noarch.rpm
```

## 3. 检查 yum 源是否安装成功

```shell
$ sudo yum repolist enabled | grep "mysql.*-community.*"
mysql-connectors-community           MySQL Connectors Community              21
mysql-tools-community                MySQL Tools Community                   38
mysql57-community                    MySQL 5.7 Community Server             130
```

## 4. 安装

```shell
$ sudo yum install mysql-community-server
```

## 5. 启动安装服务

```shell
$ sudo systemctl enable mysqld
```

##  6. 启动服务

```shell
$ sudo systemctl start mysqld
```

##  7. 查看服务状态

```shell
$ sudo systemctl status mysqld
```

## 8. 修改 root 默认密码

MySQL 5.7 启动后，在 /var/log/mysqld.log 文件中给 root 生成了一个默认密码。通过下面的方式找到 root 默认密码，然后登录 mysql 进行修改：

```shell
$ grep 'temporary password' /var/log/mysqld.log
[Note] A temporary password is generated for root@localhost: **********
```

## 8. 登录 MySQL 并修改密码

```shell
$ mysql -u root -p
Enter password: 
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```

注意：MySQL 5.7 默认安装了密码安全检查插件（validate_password），默认密码检查策略要求密码必须包含：大小写字母、数字和特殊符号，并且长度不能少于 8 位。

通过 MySQL 环境变量可以查看密码策略的相关信息：

```shell
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
```

## 9. 指定密码校验策略

```shell
$ sudo vi /etc/my.cnf

[mysqld]
# 添加如下键值对, 0=LOW, 1=MEDIUM, 2=STRONG
validate_password_policy=0
```

## 10. 禁用密码策略

```shell
$ sudo vi /etc/my.cnf
	
[mysqld]
# 禁用密码校验策略
validate_password = off
```

## 11. 重启 MySQL 服务，使配置生效

```shell
$ sudo systemctl restart mysqld
```

## 12. 添加远程登录用户

MySQL 默认只允许 root 帐户在本地登录，如果要在其它机器上连接 MySQL，必须修改 root 允许远程连接，或者添加一个允许远程连接的帐户，为了安全起见，本例添加一个新的帐户：

```shell
mysql> GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' IDENTIFIED BY 'secret' WITH GRANT OPTION;
```

## 13. 配置默认编码为 utf8

MySQL 默认为 latin1, 一般修改为 UTF-8

```shell
$ vi /etc/my.cnf
[mysqld]
# 在myslqd下添加如下键值对
character_set_server=utf8
init_connect='SET NAMES utf8'
```

## 14. 重启 MySQL 服务，使配置生效

```shell
$ sudo systemctl restart mysqld

// 查看字符集
mysql> SHOW VARIABLES LIKE 'character%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec
```

## 15. 开启防火区3306端口

```shell
$ sudo firewall-cmd --zone=public --add-port=3306/tcp --permanent
$ sudo firewall-cmd --reload
```

## 16. 卸载旧版本

```sh
1.停掉服务 service mysqld stop
2.确保所有msyql的服务进程杀死
ps -ef | grep -i mysql 查出mysql的相关进程
然后一个一个地 kill -9 进程号
3.删除mysql的rpm包
rpm -qa | grep -i mysql | xargs rpm -e --nodeps
4.删除mysql遗留的文件
find / -name mysql | xargs rm -rf
find / -name my.cnf | xargs rm -rf
```



# mysql基本命令

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

# 数据类型

## 整数类型

包括TINYINT、SMALLINT、MEDIUMINT、INT、BIGINT，分别表示1字节、2字节、3字节、4字节、8字节整数。

任何整数类型都可以加上`UNSIGNED`属性，表示数据是无符号的，即非负整数。

`长度`：整数类型可以被指定长度，例如：INT(11)表示长度为11的INT类型。它不会限制值的合法范围，只会影响显示字符的个数，而且需要和UNSIGNED ZEROFILL属性配合使用才有意义。
`例子`，假定类型设定为INT(5)，属性为UNSIGNED ZEROFILL，如果用户插入的数据为12的话，那么数据库实际存储数据为00012。

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

尽量使用timestamp，空间效率高于datetime，

用整数保存时间戳通常不方便处理。

如果需要存储微妙，可以使用bigint存储。

# 索引

## 什么是索引

索引是一种特殊的文件(InnoDB数据表上的索引是表空间的一个组成部分)，它们包含着对数据表里所有记录的引用指针。它是要占据物理空间的。

索引是一种数据结构。数据库索引，是数据库管理系统中一个排序的数据结构。索引的实现通常使用B树及其变种B+树。

## 索引有哪些优缺点

索引的优点

- 可以大大加快数据的检索速度，这也是创建索引的最主要的原因。
- 通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。

索引的缺点

- 时间方面：创建索引和维护索引要耗费时间，具体地，当对表中的数据进行增加、删除和修改的时候，索引也要动态的维护，会降低增/改/删的执行效率；
- 空间方面：索引需要占物理空间。

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

## 索引有哪几种类型

**主键索引:** 数据列不允许重复，不允许为NULL，一个表只能有一个主键。

**唯一索引:** 数据列不允许重复，允许为NULL值，一个表允许多个列创建唯一索引。

- 可以通过 `ALTER TABLE table_name ADD UNIQUE (column);` 创建唯一索引
- 可以通过 `ALTER TABLE table_name ADD UNIQUE (column1,column2);` 创建唯一组合索引

**普通索引:** 基本的索引类型，没有唯一性的限制，允许为NULL值。

- 可以通过`ALTER TABLE table_name ADD INDEX index_name (column);`创建普通索引
- 可以通过`ALTER TABLE table_name ADD INDEX index_name(column1, column2, column3);`创建组合索引

**全文索引：** 是目前搜索引擎使用的一种关键技术。

- 可以通过`ALTER TABLE table_name ADD FULLTEXT (column);`创建全文索引

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

## 索引设计的原则

1. 适合索引的列是出现在where子句中的列，或者连接子句中指定的列
2. 基数较小的类，索引效果较差，没有必要在此列建立索引
3. 使用短索引，如果对长字符串列进行索引，应该指定一个前缀长度，这样能够节省大量索引空间
4. 不要过度索引。索引需要额外的磁盘空间，并降低写操作的性能。在修改表内容的时候，索引会进行更新甚至重构，索引列越多，这个时间就会越长。所以只保持需要的索引有利于查询即可。

## 创建索引的原则

索引虽好，但也不是无限制的使用，最好符合一下几个原则

* 最左前缀匹配原则，组合索引非常重要的原则，mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配，比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。

* 较频繁作为查询条件的字段才去创建索引

* 更新频繁字段不适合创建索引

* 若是不能有效区分数据的列不适合做索引列(如性别，男女未知，最多也就三种，区分度实在太低)

* 尽量的扩展索引，不要新建索引。比如表中已经有a的索引，现在要加(a,b)的索引，那么只需要修改原来的索引即可。

* 定义有外键的数据列一定要建立索引。

* 对于那些查询中很少涉及的列，重复值比较多的列不要建立索引。

* 对于定义为text、image和bit的数据类型的列不要建立索引。

## 创建索引的三种方式，删除索引

### 第一种方式

```sql
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
```

### 第二种方式：

使用`ALTER TABLE`命令去增加索引

```sql
ALTER TABLE table_name ADD INDEX index_name (column_list);
```

ALTER TABLE用来创建普通索引、UNIQUE索引或PRIMARY KEY索引。

### 第三种方式：

使用`CREATE INDEX`命令创建

```sql
CREATE INDEX index_name ON table_name (column_list);
```

CREATE INDEX可对表增加普通索引或UNIQUE索引。（但是，不能创建PRIMARY KEY索引）

### 删除索引

```sql
alter table user_index drop KEY name;
alter table user_index drop KEY id_card;
alter table user_index drop KEY information;
```

user_index 是表名

### 删除主键索引

`alter table 表名 drop primary key`（因为主键只有一个）

这里值得注意的是，如果主键自增长，那么不能直接执行此操作（自增长依赖于主键索引),需要取消自增长再行删除

## 创建索引时需要注意

- 非空字段：应该指定列为NOT NULL，除非你想存储NULL。在mysql中，含有空值的列很难进行查询优化，因为它们使得索引、索引的统计信息以及比较运算更加复杂。你应该用0、一个特殊的值或者一个空串代替空值；
- 取值离散大的字段：（变量各个取值之间的差异程度）的列放到联合索引的前面，可以通过count()函数查看字段的差异值，返回值越大说明字段的唯一值越多字段的离散程度高；
- 索引字段越小越好：数据库的数据存储以页为单位一页存储的数据越多一次IO操作获取的数据越大效率越高。

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

## 什么是最左前缀原则？什么是最左匹配原则

* 最左优先，在创建多列索引时，要根据业务需求，where子句中使用最频繁的一列放在最左边
* =和in可以乱序，比如a = 1 and b = 2 and c = 3 建立(a,b,c)索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式
* 最左前缀匹配原则，非常重要的原则，mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配，比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。

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

## 联合索引是什么？为什么需要注意联合索引中的顺序

MySQL可以使用多个字段同时建立一个索引，叫做联合索引。在联合索引中，如果想要命中索引，需要按照建立索引时的字段顺序挨个使用，否则无法命中索引。

具体原因为:

MySQL使用索引时需要索引有序，假设现在建立了"name，age，school"的联合索引，那么索引的排序为: 先按照name排序，如果name相同，则按照age排序，如果age的值也相等，则按照school进行排序。

当进行查询时，此时索引仅仅按照name严格有序，因此必须首先使用name字段进行等值查询，之后对于匹配到的列而言，其按照age字段严格有序，此时可以使用age字段用做索引查找，以此类推。因此在建立联合索引的时候应该注意索引列的顺序，一般情况下，将查询需求频繁或者字段选择性高的列放在前面。此外可以根据特例的查询或者表结构进行单独的调整。

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

# 5.日志

## MySQL的binlog有有几种录入格式？分别有什么区别？

- statement模式下，每一条会修改数据的sql都会记录在binlog中。不需要记录每一行的变化，减少了binlog日志量，节约了IO，提高性能。由于sql的执行是有上下文的，因此在保存的时候需要保存相关的信息，同时还有一些使用了函数之类的语句无法被记录复制。
- row级别下，不记录sql语句上下文相关信息，仅保存哪条记录被修改。记录单元为每一行的改动，基本是可以全部记下来但是由于很多操作，会导致大量行的改动(比如alter table)，因此这种模式的文件保存的信息太多，日志量太大。
- mixed，一种折中的方案，普通操作使用statement记录，当无法使用statement的时候使用row。





主键

索引分单列索引和组合索引。

单列索引，即一个索引只包含单个列，一个表可以有多个单列索引，但这不是组合索引。

组合索引，即一个索引包含多个列。

创建索引时，你需要确保该索引是应用在 SQL 查询语句的条件(一般作为 WHERE 子句的条件)。

实际上，索引也是一张表，该表保存了主键与索引字段，并指向实体表的记录。

索引也会有它的缺点：虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE。因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件。建立索引会占用磁盘空间的索引文件。

有四种方式来添加数据表的索引

**ALTER TABLE tbl_name ADD PRIMARY KEY (column_list):** 该语句添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。

**ALTER TABLE tbl_name ADD UNIQUE index_name (column_list):** 这条语句创建索引的值必须是唯一的（除了NULL外，NULL可能会出现多次）。

**ALTER TABLE tbl_name ADD INDEX index_name (column_list):** 添加普通索引，索引值可出现多次。

**ALTER TABLE tbl_name ADD FULLTEXT index_name (column_list):**该语句指定了索引为 FULLTEXT ，用于全文索引。

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

5.隔离级别



幻读:重点在于新增或者删除,(条数发生了变化),如果是新增的时候,查询条数查询不到,但是可以修改,然后又能查询出出来

| 事务A                                               | 事务B                                                        |
| --------------------------------------------------- | ------------------------------------------------------------ |
| SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;    | SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;             |
| BEGIN;                                              | BEGIN;                                                       |
|                                                     | SELECT * FROM students WHERE id = 99;(没有查到数据)          |
| INSERT INTO students (id, name) VALUES (99, 'Bob'); |                                                              |
| COMMIT;                                             |                                                              |
|                                                     | SELECT * FROM students WHERE id = 99;(没有查到数据)          |
|                                                     | UPDATE students SET name = 'Alice' WHERE id = 99;(影响行数:1) |
|                                                     | SELECT * FROM students WHERE id = 99;(可以查出数据)          |
|                                                     | COMMIT;                                                      |

# 使用mysql的注意事项

1.起mysql字段名字,避开类似关键字的名字,说不定可能就会报错了

2.Mysql中DataTime 和 timestamp的区别

* 前者8字节,后者4字节
* 都是显示的是年月日时分秒
* 前者就是年月日时分秒,后者在转成Date对象,会存在时区,所以要看业务选择



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

## MyISAM索引与InnoDB索引的区别

- InnoDB索引是聚簇索引，MyISAM索引是非聚簇索引。
- InnoDB的主键索引的叶子节点存储着行数据，因此主键索引非常高效。
- MyISAM索引的叶子节点存储的是行数据地址，需要再寻址一次才能得到数据。
- InnoDB非主键索引的叶子节点存储的是主键和其他带索引的列数据，因此查询时做到覆盖索引会非常高效。

### InnoDB引擎的4大特性

- 插入缓冲（insert buffer)
- 二次写(double write)
- 自适应哈希索引(ahi)
- 预读(read ahead)

## 简化执行流程图



## 物理结构



访问控制模块  判断有没有增删改查的权限

用户模块,判断有没有访问表的权限

### 日志文件(顺序IO)



因为日志文件是新增的,不会存在浪费空间

随机IO,不会浪费空间,所以适合做增删改查



### 错误日志



### 二进制日志（bin log）
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

### 通用查询日志（general query log）

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



### 慢日志查询（slow query log）

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



慢查询日志（slow query log）
默认是关闭的。
需要通过以下设置进行开启：
记录执行时间超过long_query_time秒的所有查询，便于收集查询时间比较长的SQL语句

### 数据IO(随机IO)

# 索引

一个索引,就是一个索引树

一个组合索引,也是一个索引树



查看索引:

![image-20200102210437164](img/image-20200102210437164.png)

1. 表名
2. 0:唯一索引,1:不是唯一的
3. 索引的名称
4. 索引中的列序列号，从1开始,1,2,3这种是联合索引
5. 列名称
6. 列以什么方式存储在索引中。在MySQL中，有值‘A’（升序）或NULL（无分类）。
7. 索引中唯一值的数目的估计值。通过运行ANALYZE TABLE或myisamchk -a可以更新。基数根据被存储为整数的统计数据来计数，所以即使对于小型表，该值也没有必要是精确的。基数越大，当进行联合时，MySQL使用该索引的机会就越大
8. 如果列只是被部分地编入索引，则为被编入索引的字符的数目。如果整列被编入索引，则为NULL。
9. 指示关键字如何被压缩。如果没有被压缩，则为NULL。
10. 如果列含有NULL，则含有YES。如果没有，则该列含有NO
11. 用过的索引方法（BTREE, FULLTEXT, HASH, RTREE）

# 锁

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



