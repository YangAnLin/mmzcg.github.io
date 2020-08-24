[toc]


## Linux

## Centos8

### Cockpit

```shell
Activate the web console with: systemctl enable --now cockpit.socke
```

Cockpit介绍自己是一个Web端的系统管理工具，只用鼠标点点就能管理系统，事实上也确实如此，我实际使用来说，启动Cockpit服务之后，只需要鼠标点点点就能完成系统很多基础操作，比如查看系统信息，启动/停止服务，新增或者更改账户，系统更新，Web终端及查看网络流量等功能。

```shell
systemctl start cockpit.socket   # 运行Cockpit服务

systemctl enable –now cockpit.socket  # 启动该服务，随系统启动一同启动

systemctl status cockpit.socket
```

然后在另一台电脑上用浏览器登录 https://IP:9090





### 修改时区

```shell
# 查询当前时间
date -R  

# tzselect
选择 4,9,1,1

cp /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime

```


### sudo 命令执行的很慢，很久才出现密码输入
```shell
（1）获取你的主机名：hostname
（2）vi /etc/hosts
在里面加一个：127.0.0.1     主机名
```

### 安装ssh
```shell
1.判断Ubuntu是否安装了ssh服务,检查有没有可以看到“sshd”
ps -e | grep ssh
2.安装ssh服务，输入命令：#sudo apt-get install openssh-server 
3.启动服务:#/etc/init.d/ssh start 
```

### grep管道
```shell
cat hive-default.xml | grep hive.cli.print
```
### 安装vm tool
```shell
运行 ./vmware-install.pl
一直按yes,按完一段之后就会有暂时的停顿
运行命令 reboot
```

### 系统升级
```shell
yum -y update(centos)
apt-get update(ubuntu 软件包的列表 )
sudo apt-get dist-upgrade （进入列新你系统和系统里安装的软件）
```

### 查看前几行

```shell
ifconfig | head -n 100
```

### yum无法使用
```shell
[root@localhost network-scripts]# cd /etc/sysconfig/network-scripts
[root@localhost network-scripts]# ls
ifcfg-enp0s3  ifdown-bnep  ifdown-ipv6  ifdown-ppp     ifdown-Team      ifup          ifup-eth   ifup-isdn   ifup-post    ifup-sit       ifup-tunnel       network-functions
ifcfg-lo      ifdown-eth   ifdown-isdn  ifdown-routes  ifdown-TeamPort  ifup-aliases  ifup-ippp  ifup-plip   ifup-ppp     ifup-Team      ifup-wireless     network-functions-ipv6
ifdown        ifdown-ippp  ifdown-post  ifdown-sit     ifdown-tunnel    ifup-bnep     ifup-ipv6  ifup-plusb  ifup-routes  ifup-TeamPort  init.ipv6-global
```
```shell
vim ifcfg-enp0s3

TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=27d8efc0-8f48-44b0-9dc3-6d749af2fe6f
DEVICE=enp0s3
# 把on改成yes
ONBOOT=yes
```

### ssh连不上的问题
```shell
λ ssh root@10.19.44.12
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:RIqSp3Pd94k4PPQcDtKAzK0sQbf4VWffKGwy/nvO8Kw.
Please contact your system administrator.
Add correct host key in C:\\Users\\Owner/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in C:\\Users\\Owner/.ssh/known_hosts:12
ECDSA host key for 10.19.44.12 has changed and you have requested strict checking.
Host key verification failed.

C:\Users\Owner\Desktop
λ ssh-keygen -l -f ~/.ssh/known_hosts
ssh-keygen: ~/.ssh/known_hosts: No such file or directory

C:\Users\Owner\Desktop
λ vi  C:\\Users\\Owner/.ssh/known_hosts
```

### 查看命令打印出来的前几行

```shell
别的命令 | head -n 1000
```

### Centos 7 firewalld

```shell
启动：systemctl start firewalld
查看状态systemctl status firewalld  /firewall-cmd –state
停止：systemctl disable firewalld
禁用：systemctl stop firewalld
```

### 添加用户
```shell
root下面添加用户 adduser hadoop
删除用户 userdel hadoop
修改hadoop密码 passwd hadoop
把用户加入到用户组: vi  /etc/sudoers
```

### 修改用户权限
```shell
root修改用户权限: vim /etc/sudousers
```

### 修改主机名字
```shell
1.修改名字:sudo  vim /etc/sysconfig/network
2.编辑: HOSTNAME=weekend100
3.刷新： sudo   hostname weenkend110

# 第二种方法
vim /etc/hostname
需要重启
```

### ll文件大小按MB显示
```shell
ls -lh
ll -lh
```

### 配置Java环境变量
```shell
export JAVA_HOME=/home/anthony/soft/jdk1.8.0_19
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

### 配置Maven环境变量
```shell
export M2_HOME=/usr/local/software/apache-maven-3.6.1
export PATH=${M2_HOME}/bin:$PATH
```


### Centos安装wget
```shell
yum -y install wget
```

### 立即关机和重启
```shell
shutdown -h now 现在立即关机
shutdown -r now 现在立即重启
```

### 查看端口

```shell
lsof -i:端口号查看某个端口是否被占用 

netstat -anp|grep 80 
```

### 获取远程服务器上的文件

```shell
scp -P 2222 root@www.vpser.net:/root/lnmp0.4.tar.gz /home/lnmp0.4.tar.gz
```

* 上端口大写P 为参数，
* 2222 表示更改SSH端口后的端口，如果没有更改SSH端口可以不用添加该参数。
* root@www.vpser.net 表示使用root用户登录远程服务器www.vpser.net，
* `:/root/lnmp0.4.tar.gz` 表示远程服务器上的文件，最后面的/home/lnmp0.4.tar.gz表示保存在本地上的路径和文件名。还可能会用到p参数保持目录文件的权限访问时间等。


### 获取远程服务器上的目录

```shell
scp -P 2222 -r root@www.vpser.net:/root/lnmp0.4/ /home/lnmp0.4/
```

* 上端口大写P 为参数，2222 表示更改SSH端口后的端口，如果没有更改SSH端口可以不用添加该参数。
* -r 参数表示递归复制（即复制该目录下面的文件和目录）；
* root@www.vpser.net 表示使用root用户登录远程服务器www.vpser.net，
* `:/root/lnmp0.4/` 表示远程服务器上的目录，最后面的/home/lnmp0.4/表示保存在本地上的路径。

### 将本地文件上传到服务器上

```shell
scp -P 2222 /home/lnmp0.4.tar.gz* *root@www.vpser.net:/root/lnmp0.4.tar.gz
```

* 上端口大写P 为参数，2222 表示更改SSH端口后的端口，如果没有更改SSH端口可以不用添加该参数。 
* /home/lnmp0.4.tar.gz表示本地上准备上传文件的路径和文件名。
* root@www.vpser.net 表示使用root用户登录远程服务器www.vpser.net，
* `:/root/lnmp0.4.tar.gz` 表示保存在远程服务器上目录和文件名。

### 将本地目录上传到服务器上

```shell
scp -P 2222 -r /home/lnmp0.4/* *root@www.vpser.net:/root/lnmp0.4/
```

* 上 端口大写P 为参数，2222 表示更改SSH端口后的端口，如果没有更改SSH端口可以不用添加该参数。
* -r 参数表示递归复制（即复制该目录下面的文件和目录）；
* /home/lnmp0.4/表示准备要上传的目录，root@www.vpser.net 表示使用root用户登录远程服务器www.vpser.net，
* `:/root/lnmp0.4/`表示保存在远程服务器上的目录位置。

### 历史命令

```shell
# 历史命令
history -3
```

### 定时器

```shell
crontab -e
```

### Shell脚本

```shell
each输出

美元符号 动态获取

声明变量的后面要写等号

```

### data

```shell
data
```

### Vim行号

```shell
set nu
```

### 查看系统版本

```shell
cat /etc/os-release
```

### 开放端口

```shell
sudo ufw allow 80
```

### 查看端口

```shell
# 查看所有的服务端口
netstat -ap
# 查看已经连接的服务端口
netstat -a
# 如查看8888端口，则在终端中输入：
lsof -i:8888
```

### 安装Wget

最开始的Centos7 感觉什么都没有`wget`也没有
```shell
 #远程下载的工具
 yum -y install wget
 
 #bash: make: command not found 是因为缺少这些工具
 yum -y install gcc automake autoconf libtool make
 
 yum -y install wget
```

### 切换用户

```
su anthony
```

### Ubuntu-Nginx

```shell
ubuntu安装nginx时提示error: the HTTP rewrite module requires the PCRE library 
sudo apt-get install libpcre3 libpcre3-dev
sudo apt-get install openssl libssl-dev
```

```shell
#安装到这里
./configure --prefix=/url/local/nginx
#或者
./configure

#然后目录下就多了一个nginx文件
make && make install

#启动
cd ../nginx/sbin
./nginx

```

### Centos mini 的网络配置
修改配置文件
```shell
vi /etc/sysconfig/network-script/ifcfg-eth0

ONBOOT=yes
MM_Controlled=no
BOOTPROTO=dhcp
```

重启网卡
```shell
service network restart
```

### ubuntu右上角网络图标消失并且上不了网

```shell
sudo service network-manager stop
sudo rm /var/lib/NetworkManager/NetworkManager.state
sudo service network-manager start

sudo gedit /etc/NetworkManager/nm-system-settings.conf
把false改成true

sudo service network-manager restart
```

### 过滤注释

```shell
 cat redis.conf |grep -v '#' |grep -v '^$' >redis-6237.conf
```

## 教程

### Mysql
开启远程授权
```shell
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
```

第一次安装,查看密码
```shell
cat /var/log/mysqld.log |grep 'password'
```

修改密码
```shell
ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
```
刷新配置
```shell
FLUSH PRIVILEGES; 
```

#### 简单的操作
```导入sql文件
source /root/20151010.sql
```


#### 显示所有的表
```shell
show database
```

### Nginx

查看进行有两个进行,一个是master,主进程(用来管理子进行的),一个工作进行,

### docker启动nginx
```shell

docker run -d \
           -p 443:443 \
           -p 80:80 \
           --name nginx \
           -v /home/nginx/www:/usr/share/nginx/html \
           -v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
           -v /home/nginx/logs:/var/log/nginx \
           nginx
```

#### nginx 配置模板
```shell
 
#user  nobody;
# 设置 <= cpu核数
worker_processes  1;
 
# 指定错误日志文件存放路径，错误日志级别可选项为【debug|info|notice|warn|error|crit】
error_log  logs/warn.log  warn;
# 指定pid存放路径 nginx启动后的进程ID
pid        logs/nginx.pid;
 
 
 
# 工作模式及连接数上限
#user  nobody;
# 设置 <= cpu核数
worker_processes  1;
 
# 指定错误日志文件存放路径，错误日志级别可选项为【debug|info|notice|warn|error|crit】
# error_log  logs/error.log  error;
# 指定pid存放路径 nginx启动后的进程ID
# pid        logs/nginx.pid;
 
 
 
# 工作模式及连接数上限
events {
    # 使用网络I/O模型，Linux系统推荐使用epoll模型，FreeBSD系统推荐使用kqueue;window下不指定
    # 允许的连接数
    # user epoll;
    worker_connections  1024;
}
 
# 设定http服务器，利用他的反向代理功能提供负载均衡支持
http {
    # 设定mime类型
    include       mime.types;
    default_type  application/octet-stream;
    # 设定日志格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    client_header_buffer_size 1k;
    large_client_header_buffers 4 4k;
 
    # access_log  logs/info.log  main;
    # # 设定access log
    send_timeout 3m;
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    # 这个参数表示http连接超时时间，默认是65s。要是上传文件比较大，在规定时间内没有上传完成，就会自动断开连接！所以适当调大这个时间。
    keepalive_timeout  0;
    keepalive_timeout  5000;
    # 开启gzip模块
    gzip  on;
    gzip_min_length 1100;
    gzip_buffers 4 8k;
    gzip_types text/plain application/x-javascript text/css application/xml;
    output_buffers 1 32k; 
    postpone_output 1460;
    server_names_hash_bucket_size 128; 
    client_header_timeout 3m;        #调大点
    client_body_timeout 3m;          #调大点
    client_max_body_size 200m;         #主要是这个参数，限制了上传文件大大小
    #client_body_buffer_size 1024;   
    fastcgi_connect_timeout 300; 
    fastcgi_send_timeout 300; 
    fastcgi_read_timeout 300; 
    fastcgi_buffer_size 64k; 
    fastcgi_buffers 4 64k; 
    fastcgi_busy_buffers_size 128k; 
    fastcgi_temp_file_write_size 128k; 
    gzip_http_version 1.1; 
    gzip_comp_level 2; 
    gzip_vary on;
 
 
	#设定虚拟主机
    server {
        listen      80;
	    server_name  ap.mmzcg.com;
        
        # 设置url编码格式，解决参数中文乱码问题
        charset UTF-8;
	    # 设定本虚拟主机的访问日志
        # access_log  logs/host.access.log  main;
	
			
        # 对 "/" 启用负载均衡
        location / {
            # 设定代理访问地址
            proxy_pass http://127.0.0.1:30000/;

            # 配置SSL证书
            # ssl_certificate /etc/nginx/conf.d/ssl/STAR.huihuang200.com.crt;
            # ssl_certificate_key  /etc/nginx/conf.d/ssl/STAR.huihuang200.com.key;
            # ssl_session_timeout 5m;
            # ssl_session_cache shared:SSL:10m;
            # ssl_protocols TLSv1 TLSv1.1 TLSv1.2 SSLv2 SSLv3;
            # ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
            # ssl_prefer_server_ciphers on;
            # ssl_verify_client off;
 
            # 解决ajax跨域问题
            add_header 'Access-Control-Allow-Origin' '*';
            add_header 'Access-Control-Allow-Credentials' 'true';
            add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
            add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
			
	        # 保留用户真实信息
            proxy_set_header Host $host:$server_port; 
            proxy_set_header X-Real-IP $remote_addr; 
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			
            # 允许客户端请求的最大单个文件字节数
            client_max_body_size 100m;
            # 缓冲区代理缓冲用户端请求的最大字节数，可以理解为先保存到本地再传给用户
            client_body_buffer_size 1280k;
            # nginx跟后端服务器连接超时时间，发起握手等候响应超时时间(代理连接超时)
            proxy_connect_timeout 5;
            # 连接成功后 等待后端服务器响应时间 其实已进入后端的排队之中等候处理
            proxy_read_timeout 60;
            # 代理请求缓存区 这个缓存区间会保存用户的头信息一共Nginx进行规则处理 一般只要能保存下头信息即可
            proxy_send_timeout 30;
            # 同上 告诉Nginx保存单个用的几个Buffer最大用多大空间
            proxy_buffer_size 256k;
            proxy_buffers 4 256k;
            # 如果系统很忙的时候可以申请国内各大的proxy_buffers 官方推荐 *2
            proxy_busy_buffers_size 256k;
            # proxy 缓存临时文件的大小
            proxy_temp_file_write_size 256k; 
            proxy_next_upstream error timeout invalid_header http_500 http_503 http_404; 
            proxy_max_temp_file_size 128m;
        }
    }
 
}

```

#### 安装nginx
> 参考:https://www.cnblogs.com/liujuncm5/p/6713784.html

```shell
安装依赖
yum install gcc-c++
yum install -y pcre pcre-devel
yum install -y zlib zlib-devel
yum install -y openssl openssl-devel


编译
# 如果不修改的话,就是使用默认配置
./configure  

make
make install

运行完之后,cd /usr/local/nginx

```

#### nginx转发websocket
```shell
server{                                                    
     listen 8081;                                           
     server_name 10.10.100.125;
     
     location / {
        # 转发的时候,还是使用http协议
        proxy_pass http://10.10.100.120:8081;
        
        proxy_http_version 1.1; 
        # 转发长连接,就下面两行代码有效果         
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade"; 
    }                                              
}            
```


#### 信号量

重启和关闭,使用信号量

```shell
#强制停止
kill -int pid(master线程)
```



有这些信号量

|       |                     |                 |
| ----- | ------------------- | --------------- |
| int   | 强制关闭            |                 |
| quit  | 所有请求完毕,再关闭 |                 |
| hup   | 修改配置文件使用的  | nginx -s reload |
| user1 | 分割日志用          |                 |
| user2 | 升级重启            |                 |
| winch | 配合user2使用       |                 |



#### 虚拟主机的管理

work_processes 1;一般设置成cpu*核数

worker_connection是 1024; 一个核 最大允许多少个链接



```shell
    server {
    	#监听段鸥
        listen  80;        
        #监听域名
        server_name localhost;
        location / {
        	#my文件夹的相对路径
        	root my;
        	#在my文件夹下面找index.html
        	index index.html
       }
    }
```



#### 日志管理

```shell
#access_log  logs/access.log  main;

#log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
#                  '$status $body_bytes_sent "$http_referer" '
#                  '"$http_user_agent" "$http_x_forwarded_for"';


    server {
    	#监听段鸥
        listen  80;        
        #监听域名
        server_name localhost;
        location / {
        	#my文件夹的相对路径
        	root my;
        	#在my文件夹下面找index.html
        	index index.html
       }
       #配置日志
       access_log  logs/access.log  main;
    }
```

该server的访问日志的文件是logs/access.log,使用的是main格式



#### location匹配

=          精准匹配

不写    一般匹配

~          正则匹配



## Vim

## 知识点

### Mybatis源码

sqlSessionFactorybuilder 有个build方法,读取mybatis-confi.xml,根据配置封装Configuration对象,
build创建DefaultSqlSessionFactory(实现类) 返回的是SqlSessionFactory(接口)
DefaultSqlSessionFactory有些opensession的重载方法,有的设置是否自动提交,有的设置隔离级别,最后openSessionFromDataSource用来
创建DefaultSqlSession实现类,并且返回SqlSession接口

UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

DefaultSqlSession里的getMapper方法,调用MapperProxy代理类,有代码是判断是不是接口,如果是接口,就会生成MapperMethod对象,接着调用execute方法,把sqlSession和参数传进去
感觉最重要的来了,execute里包含所有的insert.update.delete.flush,
MapperMethod内里有个SqlCommand静态内部类,该内部类的作用是,根据配置文件封装成MappedStatement ,
MappedStatement 包含了一条sql的所有特征,
SqlSession中的Executor对象再执行update.insert等操作,MappedStatement,作为参数传进去,update操作再调用 子类SimpleExecutor的doUpdate方法
该方法里面会返回封装一个StatementHandler ,在调用StatementHandler设置参数时候，需要ParameterHandler来设置相应的参数
最后prestatmentHandler执行sql,并返回对象



### mysql事务

隔离级别

|          | 脏读 | 不可重复读 | 幻读 |
| -------- | ---- | ---------- | ---- |
| 读未提交 | √    | √          | √    |
| 读已提交 |      | √          | √    |
| 可重复读 |      |            | √    |
| 串行化   |      |            |      |




脏读 :一个事务读取到另一事务未提交的更新数据
不可重复读 : 这样就发生了在一个事务内两次读到的数据是不一样的，因此称为是不可重复读。
幻读 :一个事务读到另一个事务已提交的insert数据

传播行为
not_spport  非事务运行,有就挂起
never 有就报错
mandatory 没有就报错
require 有就有,没有就创建  | 一起成功 一起失败
support 有就有,没有就没有
require_new 新建事务，如果当前存在事务，把当前事务挂起，执行当前新建事务完成以后，上下文事务恢复再执行。
not_spport 非事务执行,有就挂起



### JVM

PermanentSpace(持久代,放静态文件，比如java类、方法等)和HeapSpace(老年代+年轻代(1个Eden区，2个Survivor区))

OOM（“OutofMemory”）异常一般主要有如下2种原因：
1.年老代溢出，表现为：java.lang.OutOfMemoryError:Javaheapspace
设置的内存参数Xmx过小或程序的内存泄露及使用不当问题。
2.持久代溢出，表现为：java.lang.OutOfMemoryError:PermGenspace
通常由于持久代设置过小，动态加载了大量Java类而导致溢出，解决办法唯有将参数-XX:MaxPermSize调大（一般256m能满足绝大多数应用程序需求）。

GC:
1.标记-清除,产生碎片
3.标记-整理（Mark-Compact）
2.复制,就是需要两倍内存空间
4.分代收集算法

Eden --> Minor GC
Eden+sur+sur --> Yougn Gen(-Xmn)
老年代 --->Major GC
年轻代+老年代 -->JVM Heap(-Xms -Xmx)