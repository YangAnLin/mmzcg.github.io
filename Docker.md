[toc]
## 教程

### 修改时区
进入容器,需要重启容器
```shell
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime 
echo "Asia/Shanghai" > /etc/timezone
```

### docker安装
```shell
# 卸载旧版本
sudo yum remove docker docker-client  docker-client-latest  docker-common  docker-latest  docker-latest-logrotate  docker-logrotate  docker-selinux  docker-engine-selinux  docker-engine
                  
# 安装
sudo yum install -y yum-utils  device-mapper-persistent-data  lvm2
           
# 添加yum源
sudo yum-config-manager --add-repo  https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
    
# 安装
yum makecache fast
yum -y install docker-ce

启动
systemctl enable docker
systemctl start docker
```

### 清空docker
```shell
# 列出所有的容器 ID
docker ps -aq

# 停止所有的容器
docker stop $(docker ps -aq)

# 删除所有的容器
docker rm $(docker ps -aq)

# 删除所有的镜像
docker rmi $(docker images -q)
```
### 开启远程访问
```shell
# 编辑
vim /lib/systemd/system/docker.service

# 添加这两行
ExecStart=
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock

# 重启
systemctl daemon-reload
systemctl restart docker
```

### 报错信息
服务没有启动,运行docker,会报这个错
```shell
docker: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?.
```
需要启动docker service
```shell
service docker start
```


### 容器


#### 容器不能上网
```shell
vim /etc/sysctl.conf
增加：net.ipv4.ip_forward=1
重启服务：systemctl restart network
查看属性是否修改成功：sysctl net.ipv4.ip_forward
```

#### 查看容器日志
```shell
docker container logs
```

#### 停止和查看容器

```shell
 #查看所有的容器(已经存在的容器,已经停止的)
 docker container ls -a
 
 #查看正在运行的容器
 docker ps
 
 #停止容器
 docker container stop 05909cd09bf9
```

#### 启动容器

```shell
 #启动已终止容器
 docker container start 05909cd09bf9
```

#### 进入容器

```shell
#进入已经启动的容器
docker attach 5cc239848ce5
docker exec -it 5cc239848ce5 bash
```

#### 停止容器

```shell
docker stop myredis
```

#### 重启容器
```shell
docker restart id
```

### 镜像

#### 运行镜像
```shell
docker run -p 10001:10001 -t springboot/eureka-item
```

```shell
docker run -it --rm ubuntu:16.04 bash
```
* docker run 运行容器的命令
* -i 交互式操作
* -t 终端
* bash 进入交互式终端
* 

#### 删除镜像
```shell
docker image rm <id>

// 删除名字是none的镜像
docker rmi $(docker images | grep "none" | awk '{print $3}') 
```

#### 查看镜像

```shell
# 查看所有镜像
docker images
```

- REPOSITORY：表示镜像的仓库源
- TAG：镜像的标签
- IMAGE ID：镜像ID
- CREATED：镜像创建时间
- SIZE：镜像大小



#### 下载新的镜像

```shell
# 直接下载
docker pull ubuntu:13.10

# 查找镜像
docker search ubuntu:13.10
```


#### 部署到私有仓库

```shell
//位置
gedit /etc/default/docker

//添加的命令
DOCKER_OPTS="–insecure-registry 172.20.100.211:5000"

//重启
service docker restart

//打tag
docker tag springboot/eureka-item 172.20.100.211:5000/anthonyfirst

//推送
docker push 172.20.100.211:5000/anthonyfirst

//获取私有仓库里的信息
curl -XGET http://172.20.100.211:5000/v2/_catalog

#客户端配置私有仓库
修改/etc/sysconfig/docker（Ubuntu下配置文件地址为：/etc/init/docker.conf），增加启动选项(已有参数的在后面追加)，之后重启docker，不添加报错，https证书问题。
OPTIONS='--insecure-registry 172.20.100.211:5000' #CentOS 7系统

#重启服务
systemctl daemon-reload
systemctl restart docker
```

## Docker和Java
### 1.缺少FontConfiguration
知道是因为alpine中缺少FontConfiguration，那么就考虑安装ttf-dejavu这个软件。

```java
java.lang.NullPointerException
    at sun.awt.FontConfiguration.getVersion(FontConfiguration.java:1264)
    at sun.awt.FontConfiguration.readFontConfigFile(FontConfiguration.java:219)
    at sun.awt.FontConfiguration.init(FontConfiguration.java:107)
    at sun.awt.X11FontManager.createFontConfiguration(X11FontManager.java:774)
    at sun.font.SunFontManager$2.run(SunFontManager.java:431)
    at java.security.AccessController.doPrivileged(Native Method)
    at sun.font.SunFontManager.<init>(SunFontManager.java:376)
    at sun.awt.FcFontManager.<init>(FcFontManager.java:35)
    at sun.awt.X11FontManager.<init>(X11FontManager.java:57)
    at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
    at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
    at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
    at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
    at java.lang.Class.newInstance(Class.java:442)
    at sun.font.FontManagerFactory$1.run(FontManagerFactory.java:83)
    at java.security.AccessController.doPrivileged(Native Method)
    at sun.font.FontManagerFactory.getInstance(FontManagerFactory.java:74)
    at java.awt.Font.getFont2D(Font.java:491)
    at java.awt.Font.access$000(Font.java:224)
    at java.awt.Font$FontAccessImpl.getFont2D(Font.java:228)
    at sun.font.FontUtilities.getFont2D(FontUtilities.java:180)
    at sun.font.StandardGlyphVector.initFontData(StandardGlyphVector.java:1126)
    at sun.font.StandardGlyphVector.init(StandardGlyphVector.java:1115)
    at sun.font.StandardGlyphVector.<init>(StandardGlyphVector.java:167)
    at java.awt.Font.createGlyphVector(Font.java:2545)
    at nl.captcha.text.renderer.DefaultWordRenderer.render(Unknown Source)
    at nl.captcha.Captcha$Builder.addText(Unknown Source)
    at com.liferay.portal.captcha.simplecaptcha.SimpleCaptchaImpl.getSimpleCaptcha(SimpleCaptchaImpl.java:243)
    at com.liferay.portal.captcha.simplecaptcha.SimpleCaptchaImpl.serveImage(SimpleCaptchaImpl.java:159)
    at com.liferay.portal.captcha.CaptchaImpl.serveImage(CaptchaImpl.java:100)
    at com.liferay.portal.kernel.captcha.CaptchaUtil.serveImage(CaptchaUtil.java:78)
    at com.liferay.portal.captcha.CaptchaPortletAction.serveResource(CaptchaPortletAction.java:42)
```

原本的dockerfile
```
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD agent-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```
要改成下面这样`RUN apk --update add fontconfig ttf-dejavu`
```
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD agent-0.0.1-SNAPSHOT.jar app.jar
RUN apk --update add fontconfig ttf-dejavu
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```



## 安装Jenkins

```shell
docker run \
--name jenkins \
--user=root \
-p 8080:8080 \
-p 50000:50000 \
-v /opt/data/jenkins_home:/var/jenkins_home \
-d \
jenkins

# 查看密码
cat /var/jenkins_home/secrets/initialAdminPassword
```

4.安装插件完之后,安装maven插件,在主机上下载maven,上传到容器中

```shell
docker cp maven-3.6.0 jenkins:/usr/local/ 

// 上传本机的配置文件 
docker cp settings.xml jenkins:/home/
```

5.进入容器

```shell
// 普通用户的权限
docker exec -it jenkins bash 

// sudo的用户权限 
docker exec -it -u 0 jenkins bash
```

6.从本机拷贝到容器,是不需要用到权限的,但是在容器内,比如从/home下的文件移动到/root 就需要权限,就需要使用 -u 0

## 安装ElasticSearch

```shell
docker run -d --name es -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:6.3.2

docker exec -it es /bin/bash

cd config

vi elasticsearch.yml

# 加入跨域配置
http.cors.enabled: true
http.cors.allow-origin: "*"

docker restart es
```

## 安装Portainer

```shell
docker volume create portainer_data

docker run -d 
		   -p 9000:9000 \
		   # 这个是必须的
		   -v /var/run/docker.sock:/var/run/docker.sock  \
		   -v portainer_data:/data portainer/portainer
```

添加docker node

https://blog.csdn.net/bj_chengrong/article/details/90300972

## 安装Redis

```shell
docker run -itd \
		   --name redis-test \
		   -p 6379:6379 \
           redis
```

## 安装Mysql

```shell
 docker run -itd --name mysql-test\
 			-p 3306:3306 \
			-e MYSQL_ROOT_PASSWORD=123456 \
			mysql
```

```shell
docker run -itd \
           --name mysql-test \
           -p 3306:3306 \
           -e MYSQL_ROOT_PASSWORD=123456 \
           -v /home/anthony/soft-config/my.cnf:/etc/mysql/conf.d/my.cnf \
           -v /home/anthony/soft-data/mysql/data:/var/lib/mysql \
           mysql
```

## 安装Nginx

```shell
docker run --name nginx-test \
		   -p 8080:80 \
		   -d \
           nginx
```

## 安装nexus3

```shell
# 创建数据文件夹
mkdir /usr/local/docker/nexus/nexus-data

# 如果有权限问题
chmod 777 /usr/local/docker/nexus/nexus-data

docker run -d \
		   -p 8081:8081 \
		   --name nexus \
		   -v /usr/local/docker/nexus/nexus-data:/nexus-data \
		   sonatype/nexus3

# 查看密码
cat /usr/local/docker/nexus/nexus-data/admin.password
```

## 安装ElasticSearch

```shell
docker run -itd \
	   -p 9200:9200 \
	   -p 9300:9300 \
	   -e "discovery.type=single-node" \
       -v /home/anthony/es/config:/usr/share/elasticsearch/config/ \
       --name elasticsearch \
       docker.elastic.co/elasticsearch/elasticsearch:7.7.0
```

