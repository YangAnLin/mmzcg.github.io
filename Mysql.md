[toc]

# 1.mysql基本命令

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



# 2.索引

# 3.锁

# 4.事务

# 5.日志

# 6.mysql安装
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



6.

```sql

```





# 7.centos7安装mysql5.7





# 8.使用mysql的注意事项

1.起mysql字段名字,避开类似关键字的名字,说不定可能就会报错了

2.Mysql中DataTime 和 timestamp的区别

* 前者8字节,后者4字节
* 都是显示的是年月日时分秒
* 前者就是年月日时分秒,后者在转成Date对象,会存在时区,所以要看业务选择



# 9.mysql集群

## 





# 存储引擎

## 简化执行流程图

![image-20200102140051735](img/image-20200102140051735.png)

## 物理结构

![image-20200102140254301](img/image-20200102140254301.png)

![image-20200102140306099](img/image-20200102140306099.png)

访问控制模块  判断有没有增删改查的权限

用户模块,判断有没有访问表的权限

### 日志文件(顺序IO)

![image-20200102141258769](img/image-202001021421258769.png)

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



