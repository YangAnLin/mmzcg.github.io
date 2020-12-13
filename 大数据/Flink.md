# 尝试flink

## 本地安装

步骤一:下载

为了能够运行Flink，唯一的要求是安装有效的**Java 8或11**。您可以通过发出以下命令来检查Java的正确安装：

```shell
# 要安装java环境
java -version

# 下载解压flink
tar -xzf flink-1.11.2-bin-scala_2.11.tgz
cd flink-1.11.2-bin-scala_2.11
```

步骤二:启动本地集群

```shell
$ ./bin/start-cluster.sh
Starting cluster.
Starting standalonesession daemon on host.
Starting taskexecutor daemon on host.
```

步骤三:提交一个job

```shell
$ ./bin/flink run examples/streaming/WordCount.jar
$ tail log/flink-*-taskexecutor-*.out
  (to,1)
  (be,1)
  (or,1)
  (not,1)
  (to,2)
  (be,2)
```

步骤四:停止集群

```shell
$ ./bin/stop-cluster.sh
```

## 使用DataStream API进行欺诈检测







