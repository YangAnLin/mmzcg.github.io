[toc]

## Vim

### 行号
```shell
set nu
```
### 过滤注释
```shell
 cat redis.conf |grep -v '#' |grep -v '^$' >redis-6237.conf
```

### 多行注释

```shell
1 Ctrl+v进入v模式
2 上下方向键选中要注释的行
3 shift+i（即大写的I）行首插入
4 输入注释符//
5 按esc返回
```

### 反注释

```shell
1 Ctrl+v进入v模式
2 上下方向键选中要注释的行，左右键选择要删除的字符//
3 按d删除
```

## SSH

### 安装ssh
```shell
1.判断Ubuntu是否安装了ssh服务,检查有没有可以看到“sshd”
ps -e | grep ssh
2.安装ssh服务，输入命令：#sudo apt-get install openssh-server 
3.启动服务:#/etc/init.d/ssh start 
```

### SSH连接,key失效

问题:

```powershell
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
```

解决方法:找到.ssh文件下的known_hosts,删除相关ip的行

### 允许SSH root远程登录

```shell
vi /etc/ssh/sshd_config 
修改AddressFamily any 前面的 # 删除
修改PermitRootLogin yes 前面的 # 删除
修改PasswordAuthentication yes 前面的 # 删除
重启ssh服务, service ssh restart
```

## 安装软件

### 安装vm tool
```shell
运行 ./vmware-install.pl
一直按yes,按完一段之后就会有暂时的停顿
运行命令 reboot
```

### 安装Wget

最开始的Centos7 感觉什么都没有`wget`也没有
```powershell
 #远程下载的工具
 yum -y install wget
 
 #bash: make: command not found 是因为缺少这些工具
 yum -y install gcc automake autoconf libtool make
 
 yum -y install wget
```

## 系统命令

### 修改时区

```shell
# 查询当前时间
date -R  

# tzselect
选择 4,9,1,1

sudo cp /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime
```

### 历史命令

```shell
# 历史命令
history -3
```

### sudo免密
`vim /etc/sudoers`
```shell
# Host alias specification
# User alias specification
# Cmnd alias specification
# User privilege specification
root    ALL=(ALL:ALL) ALL
# 加在这里会被覆盖掉
anthony ALL=(ALL) NOPASSWD:ALL 
# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL
# 最好是加在最后
anthony ALL=(ALL) NOPASSWD:ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
anthony ALL=(ALL) NOPASSWD:ALL
```

### grep管道
```shell
cat hive-default.xml | grep hive.cli.print
```

### 系统升级
```powershell
yum -y update(centos)
apt-get update(ubuntu 软件包的列表 )
sudo apt-get dist-upgrade （进入列新你系统和系统里安装的软件）
```

### ll文件大小按MB显示
```shell
ls -lh
ll -lh
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
# lsof -i:端口号查看某个端口是否被占用 
netstat -anp|grep 80 
```

### 立即关机和重启

```powershell
shutdown -h now 现在立即关机
shutdown -r now 现在立即重启
```

### 当前时间

```shell
data
```


## Cockpit

```powershell
Activate the web console with: systemctl enable --now cockpit.socke
```

Cockpit介绍自己是一个Web端的系统管理工具，只用鼠标点点就能管理系统，事实上也确实如此，我实际使用来说，启动Cockpit服务之后，只需要鼠标点点点就能完成系统很多基础操作，比如查看系统信息，启动/停止服务，新增或者更改账户，系统更新，Web终端及查看网络流量等功能。

```shell
systemctl start cockpit.socket   # 运行Cockpit服务

systemctl enable –now cockpit.socket  # 启动该服务，随系统启动一同启动

systemctl status cockpit.socket
```

然后在另一台电脑上用浏览器登录 https://IP:9090

## Linux固定IP 
配置完了,最好是重启下vmware和虚拟机,和禁用开启 win10上的vmare8网卡
vmare配置
![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226164835.png)
电脑配置
![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226164850.png)
虚拟机配置
![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226164908.png)

### Centos配置

```shell
vim /etc/sysconfig/network-scripts/ifcfg-eth0

BOOTPROTO="static" #dhcp改为static 
ONBOOT="yes" #开机启用本配置
IPADDR=10.14.2.50 #静态IP
GATEWAY=10.14.2.11 #默认网关
NETMASK=255.255.255.0 #子网掩码

# 重启网卡,或者重启电脑
service network restart
```

### Ubuntu配置

`vim /etc/netplan`

编辑完之后执行`netplan apply`

```shell
# Ubuntu桌面版本
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    ens33:
      dhcp4: no
      optional: true
      addresses: [192.168.0.5/24]
      gateway4: 192.168.0.1
      nameservers:
        addresses: [192.168.0.1,8.8.8.8]

# Ubuntu服务器版本  
network:
  ethernets:
    ens33:
      dhcp4: true
      optional: true
      addresses: [192.168.0.6/24]
      gateway4: 192.168.0.1
      nameservers:
        addresses: [192.168.0.1,8.8.8.8]
  version: 2        
        
```




## sudo 命令执行的很慢，很久才出现密码输入
```shell
（1）获取你的主机名：hostname
（2）vi /etc/hosts
在里面加一个：127.0.0.1     主机名
```


## 查看前几行

```shell
ifconfig | head -n 100
```

## yum无法使用
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

## 查看命令打印出来的前几行

```shell
别的命令 | head -n 1000
```

## Centos 7 firewalld

```shell
启动：systemctl start firewalld
查看状态systemctl status firewalld  /firewall-cmd –state
停止：systemctl disable firewalld
禁用：systemctl stop firewalld
```

## 用户和用户组

### 修改用户名

```shell
#  //-l 新的登陆名称，-d 用户新的主目录， -m将家目录内容移至新位置 (仅于 -d 一起使用)
usermod -l newname -d /home/newname -m oldname

# 修改组
groupmod -n oldname newname

# 重启
reboot
```

### 修改用户组名

```shell
# sudan新名字,dan就名字
groupmod -n susan dan
```

### 切换用户

```shell
su anthony
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

## 环境变量

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

### 卸载openjdk

```shell
# 卸载openJDK
sudo apt-get remove openjdk*
```

### 配置Scala

```shell
export SCALA_HOME=/usr/local/scala-2.13.4
export PATH=$SCALA_HOME/bin:$PATH
```

## 文件传输

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

## 定时器

```shell
crontab -e
```

## Shell脚本

```shell
each输出

美元符号 动态获取

声明变量的后面要写等号

```

## ubuntu右上角网络图标消失并且上不了网

```shell
sudo service network-manager stop
sudo rm /var/lib/NetworkManager/NetworkManager.state
sudo service network-manager start

sudo gedit /etc/NetworkManager/nm-system-settings.conf
把false改成true

sudo service network-manager restart
```

