# 有时间安装插件没有成功

![image-20210427111648745](https://blog-anthony.s3-ap-northeast-1.amazonaws.com/blog/image-20210427111648745.png)

需要到插件中心,在可更新和可选插件里,一个一个手动去搜索安装



# Jenkins配置Maven

登录到Jenkins管理后台，点击 Manage Jenkins --> Global Tool Configuration

![img](https://img2018.cnblogs.com/blog/1341090/201908/1341090-20190830104720076-103550073.png)

Install automatically 前面的勾选，去掉,输入别名和JAVA_HOME

*  注意：JAVA_HOME的输入框下面，不要有警告或者错误信息，否则就是路径不正确。使用 `mvn -version` 命令时，就已经打印了`Java home`变量，

* 注意：去除最后的jre

![img](https://blog-anthony.s3.ap-northeast-1.amazonaws.com/blog/2021/7426aa12d9b551fcbb66195d53335b6f.png)

点击Maven安装

![img](https://blog-anthony.s3.ap-northeast-1.amazonaws.com/blog/2021/e6b675711ac34f19c7e8143a0dfc6fc1.png)

![img](https://blog-anthony.s3.ap-northeast-1.amazonaws.com/blog/2021/c951418cc96b41fcd67fdc759610241c.png)

注意：使用 mvn -version 命令，就可以看到Maven home变量

MAVEN_HOME 输入框下面，不要有警告或者错误信息，否则就是路径不正确。最后点击`保存`

安装Maven Integration 插件

![img](https://blog-anthony.s3.ap-northeast-1.amazonaws.com/blog/2021/40f1da774b42d83ef00b76f932952ab1.png)