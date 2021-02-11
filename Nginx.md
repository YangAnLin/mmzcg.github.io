# Ubuntu安装Nginx

```shell
ubuntu安装nginx时提示error: the HTTP rewrite module requires the PCRE library 
```

```shell
# Ubuntu安装依赖
sudo apt-get install libpcre3 libpcre3-dev
sudo apt-get install openssl libssl-dev

# Centos安装依赖
yum install -y gcc-c++ pcre pcre-devel  zlib zlib-devel  openssl openssl-devel

#安装到这里
./configure --prefix=/url/local/nginx
#或者
./configure

#然后目录下就多了一个nginx文件
make && make install

#启动
cd ../nginx/sbin
./nginx

# 配置文件目录
所有的配置文件都在/etc/nginx下，并且每个虚拟主机已经安排在了/etc/nginx/sites-available下
程序文件在/usr/sbin/nginx
日志放在了/var/log/nginx中
并已经在/etc/init.d/下创建了启动脚本nginx
默认的虚拟主机的目录设置在了/var/www/nginx-default (有的版本 默认的虚拟主机的目录设置在了/var/www, 请参考/etc/nginx/sites-available里的配置)
```

# Docker安装Nginx
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

# Nginx 常用命令

```shell
# 启动nginx
start nginx

# 修改配置后重新加载生效
nginx -s reload

# 重新打开日志文件
nginx -s reopen

# 测试nginx配置文件是否正确
nginx -t -c /path/to/nginx.conf 

# 快速停止nginx
nginx -s stop

# 完整有序的停止nginx
nginx -s quit

# 指定配置文件启动
nginx -c /home/anthony/nginx.conf
```

# Nginx 配置模板
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

# Nginx虚拟主机的管理

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

# Nginx日志管理

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

# Nginx转发websocket
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


# 信号量

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

# location匹配

=          精准匹配

不写    一般匹配

~          正则匹配

# Nginx报错

1.服务器重启之后，执行 nginx -t 是OK的，然而在执行 nginx -s reload 的时候报错

`nginx: [error] invalid PID number "" in "/run/nginx.pid"`

解决办法:

```shell
nginx -c /etc/nginx/nginx.conf
nginx.conf文件的路径可以从nginx -t的返回中找到。
nginx -s reload
```

2.`a duplicate default server for 0.0.0.0:80`

1.nginx: [emerg] a duplicate default server for 0.0.0.0:80 in /etc/nginx/sites-enabled/gitlab:10

删除/etc/nginx/sites-available/default文件，重新启动服务即可

nginx -t :检查配置文件是否出错

