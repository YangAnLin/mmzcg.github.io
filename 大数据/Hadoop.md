# Vmware配置

配置完了,最好是重启下vmware和虚拟机,和禁用开启 win10上的vmare8网卡

1.vmare配置

![ ](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201213150357.png)

2.电脑配置

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201213150441.png)

3.虚拟机配置

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201213150509.png)

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

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201213150541.png)

8.启动hdfs

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201213150622.png)

9.启动yarn

![未命名图片](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201213150637.png)

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

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201213150653.png)

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





