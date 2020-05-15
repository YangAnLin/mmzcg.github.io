## 启动

1 .下载jre包

2 下载mycat包

3 打包镜像

Dockerfile

```shell
FROM centos
# 解压这个包到home下面
ADD server-jre-8u251-linux-x64.tar.gz /home/
ADD ./Mycat-server-1.6.7.4-release-20200105164103-linux.tar.gz /home
ENV WORKPATH /home/mycat/
WORKDIR $WORKPATH

ENV JAVA_HOME /home/jdk1.8.0_251
ENV CLASSPATH $JAVA_HOME/dt.jar:$JAVA_HOME/lib/tools.jar
ENV PATH $PATH:$JAVA_HOME/BIN:$CATALINA_HOME/lib:$CATALINA_HOME/bin



# 暴露8066端口
EXPOSE 8066
CMD /home/mycat/bin/mycat console
```
把mycat里的conf 和 logs 从容器中拷贝出来

再挂在两个目录,并启动

```shell
docker run -it \
           --name mycat \
           -p 8066:8066 \
           -v /home/anthony/mycat/conf:/home/mycat/conf/ \
           -v /home/anthony/mycat/logs:/home/mycat/logs/ \
           java1234/mycat:1.0
           
Running Mycat-server...
Removed stale pid file: /home/mycat/logs/mycat.pid
wrapper  | --> Wrapper Started as Console
wrapper  | Launching a JVM...
jvm 1    | Wrapper (Version 3.2.3) http://wrapper.tanukisoftware.org
jvm 1    |   Copyright 1999-2006 Tanuki Software, Inc.  All Rights Reserved.
jvm 1    | 
jvm 1    | MyCAT Server startup successfully. see logs in logs/mycat.log
```

## 配置

* schems.xml 定义逻辑库,表,分片节点等内容
* rule.xml 定义分片规则
* server.xml 定义用户以及系统相关变量

## 安装mysql5.7

```shell
# 从容器中拷贝这些文件
docker cp xxx:/etc/mysql/mysql.conf.d/ /home/anthony/mysql
docker cp xxx:/var/log/ /home/anthony/mysql
```

启动的时候映射这些

## 创建网络

```shell
# 可以查看容器的虚拟ip
docker inspect ac7

# 查看网络
docker network ls

# 自定义一个网络模式
# extnetwork 是网络模式的名字
# 172.20.0.1 是网关
docker network create --subnet=172.20.0.0/16 extnetwork

# 指定网络模式和ip地址
--net extnetwork --ip 172.20.0.2
```

## mysql,mycat指定网路,重建容器

```shell
docker run -itd \
            --name mysql-master \
            -p 3306:3306 \
            -e MYSQL_ROOT_PASSWORD=123456 \
            -v /home/anthony/mysql/mysql.conf.d/:/etc/mysql/conf.d/ \
            -v /home/anthony/mysql/log/:/var/log/ \
            --net extnetwork --ip 172.20.0.2 \
            mysql:5.7

docker run -itd \
            --name mysql-slave \
            -p 3307:3306 \
            -e MYSQL_ROOT_PASSWORD=123456 \
            -v /home/anthony/mysql2/mysql.conf.d/:/etc/mysql/conf.d/ \
            -v /home/anthony/mysql2/log/:/var/log/ \
            --net extnetwork --ip 172.20.0.3 \
            mysql:5.7


docker run -it \
           --name mycat \
           -p 8066:8066 \
           -v /home/anthony/mycat/conf:/home/mycat/conf/ \
           -v /home/anthony/mycat/logs:/home/mycat/logs/ \
           --net extnetwork --ip 172.20.0.4 \
           java1234/mycat:1.0
```

## 主从配置

主,在[mysqld]

```shell
# 主服务器ID,必须唯一
server-id=2
# 开启和设置二进制日志文件名称
log_bin=mysql-bin
# 要同步的数据库
binlog-do-db=db_java1234
# 不需要同步的数据库
binlog-ignore_db=mysql
binlog-ignore_db=information_schema
binlog-ignore_db=performation_schema
binlog_ignore_db=sys
# 设置logbin格式,mysql默认采用statement,建议使用mixed
binlog_format=MIXED
```

从,在[mysqld]

```shell
# 主服务器ID,必须唯一
server-id=3
# 这行是从库需要添加的
relay-log=mysql-relay

# 开启和设置二进制日志文件名称
log_bin=mysql-bin
# 要同步的数据库
binlog-do-db=db_java1234
# 不需要同步的数据库
binlog-ignore_db=mysql
binlog-ignore_db=information_schema
binlog-ignore_db=performation_schema
binlog_ignore_db=sys
# 设置logbin格式,mysql默认采用statement,建议使用mixed
binlog_format=MIXED
```

## 测试

主从还没有生效

```shell
mysql> show master status;
+------------------+----------+--------------+--------------------------------------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB                                 | Executed_Gtid_Set |
+------------------+----------+--------------+--------------------------------------------------+-------------------+
| mysql-bin.000003 |      357 | db_java1234  | mysql,information_schema,performation_schema,sys |                   |
+------------------+----------+--------------+--------------------------------------------------+-------------------+
1 row in set (0.01 sec)

mysql> show slave status;
Empty set
```

## 设置用户名

在master执行

```shell
# 创建用户名为slave1
# 172.20.0.3从库ip  
# 123456 密码
CREATE USER 'slave1'@'172.20.0.3' IDENTIFIED BY '123456'

# 语法:GRANT privileges ON databasename.tablename TO 'username'@'host'
# 172.20.0.3 从库
# 两个权限:REPLICATION和SLAVE
# *.* 所有的库和表
GRANT REPLICATION SLAVE ON *.* TO 'slave1'@'172.20.0.3'

# 刷新权限
FLUSH PRIVILEGES
```

在slave执行

```shell
CHANGE MASTER TO MASTER_HOST='172.20.0.2',MASTER_USER='slave1',MASTER_PASSWORD='123456',MASTER_LOG_FILE='mysql-bin.000003',MASTER_LOG_POS=964;

# 开启主从
start slave;
```



在从库执行

```shell
show slave status;
```

![image-20200515164906009](http://image.yanganlin.com/blog/20200515164908.png)

主要看Slave_io_running =yes

主要看Slave_sql_running =yes

