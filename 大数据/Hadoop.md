# Vmware配置

配置完了,最好是重启下vmware和虚拟机,和禁用开启 win10上的vmare8网卡

1.vmare配置

![ ](https://cdn.jsdelivr.net/gh/YangAnLin/images/20201212204544.png)

2.电脑配置

![](https://raw.githubusercontent.com/YangAnLin/images/master/20201202103954.png)

3.虚拟机配置

![](https://raw.githubusercontent.com/YangAnLin/images/master/20201202103932.png)

# Hadoop伪集群

* 关闭防火墙
* 用普通用户登录并且操作,需要权限的时候也是普通用户下用`sudo`
* 配置`vmware`,用`Nat`网络

1.``vim hadoop-env.sh`

修改,JAVA_HOME地址

2.``vim core-site.xml`

先创建hadoop-2.4.1文件下的data文件

```xml
<configuration>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/home/anthony/hadoop-2.4.1/data/</value>
    </property>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://weekend110:9000</value>
    </property>
</configuration>
```

3.``hdfs-site.xml`

```xml
<!-- 分片 -->
<property>
    <name>dfs.replication</name>
    <value>1</value>
</property>
<property>
    <name>dfs.namenode.name.dir</name>
    <value>file:///usr/data/hadoop/dfs/name</value>
</property>
<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:///usr/data/hadoop/dfs/data</value>
</property>
<property>
    <name>dfs.permissions</name>
    <value>false</value>
</property>
```

4.``vim mapred-site.xml`

```xml
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
```

5.``vim yarn-site.xml`

```xml
<property>
    <name>yarn.resourcemanager.hostname</name>
    <value>weekend110</value>
</property>
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
```

6.配置hadoop环境变量

```shell
export HADOOP_HOME=/home/anthony/hadoop-2.4.1
export PATH=$PATH:$HADOOP_HOME/bin
```

7.测试hadoop,并且格式化`namenode`

```shell
hadoop namenode -format
```

![20/12/01  20/12/01  20/12/01  20/12/01  20/12/01  20/12/01  20/12/01  20/12/01  20/12/01  20/12/01  20/12/01  20/12/01  20/12/01  20/12/01  20/12/01  20/12/01  20 12 01  0/12/01  20/12/01  20/12/01  08:01  08:01  08:01  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  INFO  util .GSet: capacity  2A20 = 1048576 entries  namenode.NameNode: Caching file names occuring more than 1€ times  util .GSet: Computing capacity for map cachedBlocks  util.GSet: VM type  64 -bit  .GSet: €.25* max menory 889 MB = 2.2 MB  util .GSet: capacity  2A18 = 262144 entries  namenode.FSNamesystan: dfs .namenode.safanode.threshold-pct  namenode.FSNamesystan: dfs .namenode.safanode.min .datanodes  namenode.FSNamesystan: dfs.namenode.safanode.extension  namenode.FSNamesystan: Retry cache on namenode is enabled  €.999@@@€128746€33  namenode.FSNamesystan: Retry cache will use 0.03 of total heap and retry cache entry expiry time is millis  util .GSet: Computing capacity for map NameNodeRetryCache  util .GSet: VM type  64-bit  util.GSet: 6.029999999329447746% max menory 889 MB = 273.1 KB  util .GSet: capacity  2A15 = 32768 entries  namenode.AclConfigFlag: ACLs enabled? false  namenode.FS1ma e: Allocated new BlockP0011d: BP-1051719723-192.168.2.20€-1606827665076  common .Storage: Storage directory /home/anthony/hadoop-2 .4.1/data/dfs/name has been successfully fomatted.  nameno e.  o rag  en Ion anager:  Olng o r aln Images WI  util .ExitlJti1: Exiting with status €  namenode.NameNode: SHIJTDCM•Q MSG:  SHUTDOWN MSG: Shutting down NameNode at weekend110/192.168.2.200  [anthony@weekend110 hadoop-2.4.1]$ ](https://raw.githubusercontent.com/YangAnLin/images/master/20201202103056.png)

8.启动hdfs

![[anthony@weekend110 current]$ tart-dfs . sh  Java HotSpot(TM) 64-Bit Server  ou have loaded library /home/anthony/hadoop-2.4.1/lib/native/libhadoop.so.1.€.€ which might have disabled stack guard.  warning:  The VM will try to fix the stack guard now.  It's highly recommended that you fix the library with •execstack -c or link it with  -z noexecstack .  20/12/01 08:04:19 WAR•Q util .NativeCodeLoader: Unable to load native-hadoop library for your platfom...  using builtin-java classes where applicable  Starting namenodes on [weekend110]  The authenticity of host •weekend110 (192.168.2.2@€) can •t be established.  ECDSA key fingerprint is  Are you sure you want to continue connecting (yes/no/[fingerprint])? yes  weekend110: Warning: Permanently added •weekend11€.192.168.2.2@€• (ECDSA) to the list of known hosts.  anthonyueekend110's password:  weekend110: starting namenode, logging to /home/anthony/hadoop-2.4.1/logs/hadoop-anthony-namenode-weekend11€.out  weekend110: Java HotSpot(TM) 64-Bit Server VM warning: You have loaded library /home/anthony/hadoop-2.4.1/lib/native/libhadoop.so.1.€.€ which might have disabled  stack guard. The VM will try to fix the stack guard now.  weekend110: It •s highly recommended that you fix the library with •execstack -c or link it with  -z noexecstack .  The authenticity of host • localhost :1)• can't be established.  ECDSA key fingerprint is SHA256:8/HDFx8GnPxtvBfænixbUZbYbosc+0J+KMHnqGX9d  Are you sure you want to continue connecting (yes/no/[fingerprint])? yes  localhost: Warning: Pemanently added •localhost• (ECDSA) to the list of known hosts.  password:  localhost: starting datanode, logging to /home/anthony/hadoop-2.4.1/logs/hadoop-anthony-datanode-weekend11€.out  localhost: Java HotSpot(TM) 64-Bit Server VM warning: You have loaded library /home/anthony/hadoop-2.4.1/lib/native/libhadoop.so.1.€.€ which might have disabled s  tack guard. The VM will try to fix the stack guard now.  localhost: It's highly recommended that you fix the library with •execstack -c or link it with  -z noexecstack .  Starting secondary namenodes [0.0.0.0]  The authenticity of host '0.0.0.0 (0.0.0.0)' can't be established.  ECDSA key fingerprint is SHA256:8/HDFx8GnPxtvBfænixbUZbYbosc+0J+KMHnqGX9d  Are you sure you want to continue connecting (yes/no/[fingerprint])? yes  0.0.0.0: Warning: Permanently added '0.0.0.0' (ECDSA) to the list of known hosts.  anthony@€.0.0.0's password:  0.0.0.0: starting secondarynamenode, logging to /home/anthony/hadoop-2.4.1/logs/hadoop-anthony-secondarynamenode-weekend11€.out  0.0.0.0: Java HotSpot(TM) 64-Bit Server VM warning: You have loaded library /home/anthony/hadoop-2.4.1/lib/native/libhadoop.so.1.€.€ which might have disabled sta  Ck guard. The VM will try to fix the stack guard now.  0.0.0.0: It's highly recommended that you fix the library with •execstack -c or link it with  -z noexecstack• .  Java HotSpot(TM) 64-Bit Server VM warning: You have loaded library /home/anthony/hadoop-2.4.1/lib/native/libhadoop.so.1.0.0 which might have disabled stack guard.  The VM will try to fix the stack guard now.  It's highly recommended that you fix the library with •execstack -c or link it with  -z noexecstack .  20 12 01 08:04:53 WARN util .NativeCodeLoader: Unable to load native-hadoop library for your platfom...  using builtin-java classes where applicable  [anthony@weekend110 current]$ jps  1890 SecondaryNameNode  1734 DataNode  1614 NameNode ](https://raw.githubusercontent.com/YangAnLin/images/master/20201202102639.png)

9.启动yarn

![未命名图片](https://raw.githubusercontent.com/YangAnLin/images/master/20201202102942.png)

10.访问web界面

```python
# hdfs界面
http://192.168.2.200:50070/
        
# hadoop界面
http://192.168.2.200:8088/cluster   
```

11.测试上传文件

```shell
# jdk-8u152-linux-x64.tar.gz 本地文件
hadoop fs -put jdk-8u152-linux-x64.tar.gz hdfs://weekend110:9000/
```

![lantnon  S naaoop  Ts -put  Linux-x  04. tar.gz  // LocaLnost:9  Java HotSpot(TM) 64-Bit Server VM warning: You have loaded library /home/anthony/hadoop-2.4.1/lib/native/libhadoop.so.1.€.€ which might have disabled stack guard.  The VM will try to fix the stack guard now.  It's highly recommended that you fix the library with •execstack -c or link it with  -z noexecstack .  20/12/01 08:11:23 WARN util .NativeCodeLoader: Unable to load native-hadoop library for your platfom...  using builtin-java classes where applicable  put: Cau From weekend110/192 .168.2.200 to failed on connection exception: java.net.ConnectException: Connection refused; For more details see:  ht ](https://raw.githubusercontent.com/YangAnLin/images/master/20201202103416.png)

12.测试下载文件

```shell
hadoop fs -get hdfs://192.168.2.200:9000/jdk-8u152-linux-x64.tar.gz
```

13.wordCount官方例子

```shell
cd /home/anthony/hadoop-2.4.1/share/hadoop/mapreduce

# 是个算pi的程序
hadoop jar hadoop-mapreduce-examples-2.4.1.jar pi 5 5
```

# Win10开发

* 还是从已经配置好服务器 拷贝到本地

各种坑:

1.缺少hadoop.dll和winutils.exe文件

```shell
https://github.com/steveloughran/winutils/tree/master/hadoop-2.8.1
```

下载`hadoop.dll`,放在`HADOOP_HOME/bin/`  和 `C:\Windows\System32` 下

下载`winutils.exe`,放在`HADOOP_HOME/bin`下

2.配置windows下的hadoop-env.sh的java_home

```shell
# Program Files  = Progra~1
# Program Files (x86)  = Progra~2
JAVA_HOME=C:\Progra~2\Java\jdk1.8.0_102
```

3.`Exception: org.apache.hadoop.io.nativeio.NativeIO$Windows.access0(Ljava/lang/String;I)Z`版本兼容问题

在项目下新建个`org.apache.hadoop.io.nativeio`包,拷贝框架里的的`NativeIO.java`到新建的目录

修改相关代码

```java
public static boolean access(String path, NativeIO.Windows.AccessRight desiredAccess) throws IOException {
    //            return access0(path, desiredAccess.accessRight());
    return true;
}
```

简单例子

```java
public class HdfsUtil {

    public static void main(String[] args) throws IOException {

        Configuration configuration = new Configuration();
        System.setProperty("hadoop.home.dir", "C:\\develop\\hadoop-2.4.1");
        System.setProperty("hadoop.HADOOP_USER_NAME.dir", "root");
        configuration.addResource(new Path("./core-site.xml"));
        configuration.set("fs.defaultFS", "hdfs://192.168.2.200:9000");
        
        FileSystem fs = FileSystem.get(configuration);
        Path path = new Path("hdfs://192.168.2.200:9000/jdk-8u152-linux-x64.tar.gz");
        final FSDataInputStream in = fs.open(path);
        final FileOutputStream os = new FileOutputStream("D:\\jdk-8u152-linux-x64.tar.gz");
        IOUtils.copy(in, os);
    }
}
```

WordCount例子

WCMapper.java

```java
import org.apache.commons.lang.StringUtils;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

import java.io.IOException;

// 4个泛型仲,1:输入key的类型,2输入的value类型,4
public class WCMapper extends Mapper<LongWritable, Text,Text,LongWritable> {


    // 每读一行,调用改方法
    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {

        final String s = value.toString();


        final String[] words = StringUtils.split(s, " ");


        for(String word:words){
            context.write(new Text(word),new LongWritable(1));
        }
    }
}
```

WCReducer

```java
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

import java.io.IOException;

public class WCReducer extends Reducer<Text, LongWritable,Text,LongWritable> {

    @Override
    protected void reduce(Text key, Iterable<LongWritable> values, Context context) throws IOException, InterruptedException {
        // 算出总次数
        long count = 0;
        for (LongWritable value : values) {
            count += value.get();
        }
        context.write(key,new LongWritable(count));
    }
}

```

WCRunner

```java
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

import java.io.IOException;

public class WCRunner {

    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
        Configuration configuration = new Configuration();
        System.setProperty("hadoop.home.dir", "C:\\develop\\hadoop-2.4.1");
        System.setProperty("hadoop.HADOOP_USER_NAME.dir", "root");

        final Job job = Job.getInstance(configuration);
        // 设置真个job所用的类在哪里NativeIO
        job.setJarByClass(WCRunner.class);

        // job使用的mapper和reduce
        job.setMapperClass(WCMapper.class);
        job.setReducerClass(WCReducer.class);

        // 指定reduce的输出类型kv类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(LongWritable.class);

        // 指定mapper的输出类型kv类型
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(LongWritable.class);

        // 指定原始数据存放在哪里
        FileInputFormat.setInputPaths(job, new Path("C:\\develop\\hadoop-2.4.1\\share\\doc\\hadoop\\hadoop-project-dist\\hadoop-common\\core-default.xml"));

        // 处理结果的存放路径
        FileOutputFormat.setOutputPath(job, new Path("d:\\count.txt"));

        // 提交给集群
        job.waitForCompletion(true);

    }
}
```





