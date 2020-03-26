# tomcat

bin:可执行文件

conf:配置文件(server.xml)

lib:tomcat依赖的jar文件

log:日志文件

temp:临时文件

webapps:可执行的项目,项目开发完成的,就放在这里面

* doc
* exmaple(如果是http://localhost:8080/example),访问的是这是
* host-manager
* manager
* ROOT(如果是http://localhost:8080),访问的是这是


# tomcat虚拟路径

## 方式一(需要重启)
在server.xml-->Engine-->Host
docBase是文件的绝对路径
path 是相对路径,是相对于webapps
下添加一个节点<Context docBase = "" path = "" />
举个例子<Context docBase = "c:/demo" path = "/demo" />,当访问localhost:8080/demo2 的时候就会进入到c:/demo的文件夹里

## 方式二
在apache-tomcat-8.5/conf/Catalina/localhost下面创建一个 项目名.xml(demo.xml)
然后写<Context docBase = "c:/demo" path = "/demo" />

# tomcat虚拟主机
在server.xml 多配置一个host
<Host appBase="c:/demo" name = "www.jd.com">
    <Context docBase = "c:/demo" path = "/demo" />
</Host>

再修改Engine标签里的Default改成 www.jd.com
还需要改hosts

# Jsp执行流程
jsp-->java-->class
比如我在apache-tomcat-8.5/webapps/demo/index.jsp 这里建这个文件
第一次运行的时候,tomcat会把index.jsp 拷贝到apache-tomcat-8.5/conf/Catalina/localhost/demo下
并且修改成index_jsp.java和index_jsp_class文件,第一次访问的时候需要编译,有点慢,第二次访问的时候就直接访问class

# / 的作用
在web.xml 里的 / 代表项目根路径
http://localhost:8080/jspDemo/

在jsp中代表的是 服务器根路径
http://localhost:8080


# Servlet生命周期
初始化 init() 该方法会在Serevlet被加载兵实例化以后执行
服务 service() ->doGet() doPost()   :访问几次,service就被调用几次
销毁 destory() Servlet被系统回收时执行 : 关闭tomcat服务时,执行一次

如果在tomcat启动时初始化要设置
## 2.5 的时候要再webl.xml的
<Servlet>
    <load-on-startup>1</load-on-startup>
</Servlet>
1 代表的最先执行,因为会有多个<Servlet>

## 3.0 
要再@WebServlet(value = "/demo",loadOnStartup =1)





