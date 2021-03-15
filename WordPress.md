# Docker安装

安装

```shell
# 安装docker-compose
apt install docker-compose 
# 查看版本
docker-compose version 
vim docker-compose.yml
```

```yml
version: '3.3'
services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress
   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
volumes:
    db_data: {}
```

```shell
# 后台运行
docker-compose -f docker-compose.wordpress.yml up -d
# 停止并删除服务
docker-compose -f docker-compose.wordpress.yml down 
# IP:8000
# IP:8000/wp-admin
```

```nginx
# ningx反代WordPress
server {
  listen 80;
  listen [::]:80;

  # 域名
  server_name .leozl.site;

  location / {
    ## service 的名称，docker-compose.yaml文件中的wordpress服务名称
    proxy_pass http://wp;

        proxy_http_version    1.1;
        proxy_cache_bypass    $http_upgrade;

        proxy_set_header Upgrade            $http_upgrade;
        proxy_set_header Connection         "upgrade";
        proxy_set_header Host                $host;
        proxy_set_header X-Real-IP            $remote_addr;
        proxy_set_header X-Forwarded-For    $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto    $scheme;
        proxy_set_header X-Forwarded-Host    $host;
        proxy_set_header X-Forwarded-Port    $server_port;
  }
}
```

# 恒创上安装

|             |               |
| ----------- | ------------- |
| 数据库:     | yanganli_blog |
| 主机名:     | localhost     |
| 数据库帐户: | yanganli_blog |
| 密码:       | lee22259669   |

# Vultr上安装



[上传备份](https://import.wp-migration.com/)

安装SSL

```shell
# 安装证书
certbot --nginx --redirect -d blog.airports.ink -m y950727@gmail.com --agree-tos --no-eff-email 

# 配置证书
vim /etc/nginx/conf.d/wordpress_http.conf
vim /etc/nginx/conf.d/wordpress_https.conf

server{
	ssl_certificate /etc/nginx/ssl/server.crt;                                                                              
	ssl_certificate_key /etc/nginx/ssl/server.key; 
}   
```



