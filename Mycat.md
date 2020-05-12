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

从容器中拷贝这些文件

docker cp xxx:/etc/mysql/mysql.conf.d/ /home/anthony/mysql
docker cp xxx:/var/log/ /home/anthony/mysql

启动的时候映射这些

