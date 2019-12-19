[toc]

# Mysql基本命令

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



# Centos7Yum安装Mysql5.7

**下载 mysql 源安装包**

```shell
$ curl -LO http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
```

**安装 mysql 源**

```shell
$ sudo yum localinstall mysql57-community-release-el7-11.noarch.rpm
```

**检查 yum 源是否安装成功**

```shell
$ sudo yum repolist enabled | grep "mysql.*-community.*"
mysql-connectors-community           MySQL Connectors Community              21
mysql-tools-community                MySQL Tools Community                   38
mysql57-community                    MySQL 5.7 Community Server             130
```

**安装**

```shell
$ sudo yum install mysql-community-server
```



**启动安装服务**

```shell
$ sudo systemctl enable mysqld
```



**启动服务**

```shell
$ sudo systemctl start mysqld
```



**查看服务状态**

```shell
$ sudo systemctl status mysqld
```



**修改 root 默认密码**

MySQL 5.7 启动后，在 /var/log/mysqld.log 文件中给 root 生成了一个默认密码。通过下面的方式找到 root 默认密码，然后登录 mysql 进行修改：

```shell
$ grep 'temporary password' /var/log/mysqld.log
[Note] A temporary password is generated for root@localhost: **********
```

登录 MySQL 并修改密码

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



**指定密码校验策略**

```shell
$ sudo vi /etc/my.cnf

[mysqld]
# 添加如下键值对, 0=LOW, 1=MEDIUM, 2=STRONG
validate_password_policy=0
```



**禁用密码策略**

```shell
$ sudo vi /etc/my.cnf
	
[mysqld]
# 禁用密码校验策略
validate_password = off
```



**重启 MySQL 服务，使配置生效**

```shell
$ sudo systemctl restart mysqld
```



**添加远程登录用户**

MySQL 默认只允许 root 帐户在本地登录，如果要在其它机器上连接 MySQL，必须修改 root 允许远程连接，或者添加一个允许远程连接的帐户，为了安全起见，本例添加一个新的帐户：

```shell
mysql> GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' IDENTIFIED BY 'secret' WITH GRANT OPTION;
```



**配置默认编码为 utf8**

MySQL 默认为 latin1, 一般修改为 UTF-8

```shell
$ vi /etc/my.cnf
[mysqld]
# 在myslqd下添加如下键值对
character_set_server=utf8
init_connect='SET NAMES utf8'
```



**重启 MySQL 服务，使配置生效**

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



**开启端口**

```shell
$ sudo firewall-cmd --zone=public --add-port=3306/tcp --permanent
$ sudo firewall-cmd --reload
```



**卸载旧版本**

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



# 使用Mysql的注意事项

1.起mysql字段名字,避开类似关键字的名字,说不定可能就会报错了

2.Mysql中DataTime 和 timestamp的区别

* 前者8字节,后者4字节
* 都是显示的是年月日时分秒
* 前者就是年月日时分秒,后者在转成Date对象,会存在时区,所以要看业务选择

