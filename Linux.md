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

### 安装LNMP环境

```shell
# 更新源
apt-get update && apt-get dist-upgrade -y

# 安装nginx
apt-get install nginx

# 安装php-fpm和常用php扩展
apt-get install php-fpm php-gd php-mbstring php-curl php-xml php-mcrypt php-mysql php-zip php-json php-redis php-memcached

# 安装mysql
apt-get install mysql-server

# 建立测试站点
# 1.我们在/var/www下面新建一个test目录，作为站点目录。 运行以下命令：
mkdir /var/www/test

# 2.新建php入口文件
echo '<?php echo 1;'  > /var/www/test/index.php

# 3.授权给fpm用户www-data，使fpm进程可以访问站点文件
chown -R www-data:www-data /var/www/test && chmod -R 755 /var/www/test

# 4.设置nginx站点配置,在/etc/nginx/conf.d新增一个test.conf文件，并写入以下内容
# 这个配置表示站点监听80端口，网站根目录为/var/www/test,入口文件为index.php,通过php-fpm进程来执行php脚本。
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /var/www/test;
        index index.php index.html index.htm;
        server_name _;
        location / {
                try_files $uri $uri/ =404;
        }
        location ~ \.php$ {
               include snippets/fastcgi-php.conf;
               fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        }
}

# 5.重新加载
nginx -t && nginx -s reload

# 6.访问试试看
```

### Ubuntu卸载Apache

```shell
# 删除apache
$ sudo apt-get --purge remove apache2
$ sudo apt-get --purge remove apache2.2-common
$ sudo apt-get autoremove

# 找到没有删除掉的配置文件，一并删除
$ sudo find  /etc -name "*apache*" -exec  rm -rf {} \;
$ sudo rm -rf /var/www

# 重装apache2
$ sudo apt-get install apache2
$ sudo /etc/init.d/apache2 restart
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

### 普通用户和非普通用户

```shell
$ 是普通用户
# 不是普通用户
```

### 修改用户名

```shell
#  //-l 新的登陆名称，-d 用户新的主目录， -m将家目录内容移至新位置 (仅于 -d 一起使用)
usermod -l newname -d /home/newname -m oldname

# 修改组
groupmod -n oldname newname

# 重启
reboot
```

### 用户组

```shell
# 新建用户组
groupadd hr

# 查看用户组
[root@192 ~]# tail -1 /etc/group
hr:x:1001:

# 修改用户组名,sudan新名字,dan就名字
groupmod -n susan dan

# 删除用户组
groupdel hr

```

### 用户

```shell
# 添加用户:useradd  hadoop,创建用户未指定任何选项,系统会创建一个同名的用户组
# 添加用户
adduser hadoop

# 删除用户,
# -r 同时也删除这个用户的主目录
userdel -r hadoop

# 修改hadoop用户密码
passwd hadoop
# 修改自己的用户名密码
passwd

# 把用户加入到用户组: 
vi /etc/sudoers

# 查看用户
id user01
# 或者
whoami
# 或者
cat /etc/shadow

# 切换用户
su anthony

# 查看用户组信息
[root@192 ~]# tail -2 /etc/passwd
anthony:x:1000:1000:anthony:/home/anthony:/bin/bash
[root@192 ~]# tail  /etc/group
anthony:x:1000:anthony

# 修改用户基本组
usermod username -g 组名
```

![image-20210111210640226](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20210111210641.png)



### 修改用户基本组和附加组的demo

```shell
# 创建hadoop用户
[root@192 ~]# useradd hadoop
# 查看hadoop用户,基本组是1002
[root@192 ~]# tail -1 /etc/passwd
hadoop:x:1001:1002::/home/hadoop:/bin/bash
# 查看hadoop基本组信息
[root@192 ~]# tail -1 /etc/group
hadoop:x:1002:

# 创建两个用户组
[root@192 ~]# groupadd hadoop_base
[root@192 ~]# groupadd hadoop_more
# 查看两个用户组
[root@192 ~]# tail -3 /etc/group
hadoop:x:1002:
hadoop_base:x:1003:
hadoop_more:x:1004:

# 修改用户基本组
[root@192 ~]# usermod hadoop -g hadoop_base
[root@192 ~]# tail -3 /etc/group
hadoop:x:1002:
hadoop_base:x:1003:
hadoop_more:x:1004:
# 用户的基本组已经变成1003
[root@192 ~]# tail -3 /etc/passwd
hadoop:x:1001:1003::/home/hadoop:/bin/bash

# 修改用户附加组
[root@192 ~]# usermod hadoop -G hadoop_more
# 用户的附加组已经变成1004
[root@192 ~]# tail -1 /etc/group
hadoop_more:x:1004:hadoop

# 最后查看
# uid 用户名
# gid 基本组id
# 组 包括基本组和附加组
[root@192 ~]# id hadoop
uid=1001(hadoop) gid=1003(hadoop_base) 组=1003(hadoop_base),1004(hadoop_more)

# 从组中删除成员
[root@192 ~]# gpasswd -d hadoop hadoop_base
正在将用户“hadoop”从“hadoop_base”组中删除
gpasswd：用户“hadoop”不是“hadoop_base”的成员
[root@192 ~]# gpasswd -d hadoop hadoop_more
正在将用户“hadoop”从“hadoop_more”组中删除
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

### 组信息文件

```shell
[root@192 ~]# cat /etc/group
root:x:0:
bin:x:1:
daemon:x:2:
sys:x:3:
adm:x:4:
```

一共有4列

1. 组名
2. 组密码
3. 组ID
4. 备用的

### 用户文件信息

```shell
[root@192 ~]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
```

1. 用户名,登录名中不能有冒号
2. 口令,把真正的加密后的用户口令字存放到/etc/shadow文件中
3. 用户标识符,取值范围是0-65535。0是超级用户root的标识号，1-99由系统保留，作为管理账号，普通用户的标识号从100开始。在Linux系统中，这个界限是500,
4. 组标识符,也是基本组
5. 注释性描述,例如用户的真实姓名、电话、地址等，这个字段并没有什么实际的用途
6. 主目录
7. 登录shell,解释器

## 权限

```shell
[anthony@192 ~]$ touch /tmp/demo.txt
[anthony@192 ~]$ ll /tmp/demo.txt
-rw-rw-r--. 1 anthony anthony 0 1月  11 21:57 /tmp/demo.txt
```

* 第一位
  * -代表文件,d代表文件夹
  * 接下来的三位(rw-),是用户权限
  * 接下来的三位(rw-),是组权限
  * 接下来的三位(r--),是其他人权限
  * . 不知道是啥东西
* 第二位,连接
* 第三位,属主
* 第四位,属组
* 第五位,大小
* 第六位,7位,时间
* 第八位,名字

## 提升权限

```shell
[root@192 tmp]# touch fil1.txt

[root@192 tmp]# ll
-rw-r--r--. 1 root root 0 1月  12 21:56 fil1.txt

# 给所有人和用户都设置读写执行的权限
[root@192 tmp]# chmod a=rwx fil1.txt

[root@192 tmp]# ll
-rwxrwxrwx. 1 root root 0 1月  12 21:56 fil1.txt

# 给所有人和用户设置读写权限
[root@192 tmp]# chmod a=rx fil1.txt

[root@192 tmp]# ll
-r-xr-xr-x. 1 root root 0 1月  12 21:56 fil1.txt

# 给属主设置读权限
[root@192 tmp]# chmod u=r fil1.txt
[root@192 tmp]# ll
-r--r-xr-x. 1 root root 0 1月  12 21:56 fil1.txt

# 给组设置读写执行的权限
[root@192 tmp]# chmod g=rwx fil1.txt
[root@192 tmp]# ll
-r--rwxr-x. 1 root root 0 1月  12 21:56 fil1.txt
[root@192 tmp]#

# 给其他人 没有读写执行的权限
[root@192 tmp]# chmod o= fil1.txt
[root@192 tmp]# ll
-r--rwx---. 1 root root 0 1月  12 21:56 fil1.txt

# 给属主单独设置一个写的权限
[root@192 tmp]# chmod u+w fil1.txt
[root@192 tmp]# ll
总用量 0
-rw-rwx---. 1 root root 0 1月  12 21:56 fil1.txt

# 修改文件的属主和组权限
[root@192 tmp]# chown hadoop.hr fil1.txt
[root@192 tmp]# ll
总用量 0
-rw-rwx---. 1 hadoop hr 0 1月  12 21:56 fil1.txt

# 查看acl权限
[root@192 tmp]# getfacl acldemo.txt
# file: acldemo.txt
# owner: root
# group: root
user::rw-
group::r--
other::r--

# 当对一个文件或者文件夹需要设置3个以上的权限用户,和权限用户组,上面的那种方法就不够用了
# 需要引入 acl权限
# setfacl -m 对象:对象名:权限 文件名
# ll之后就会先是+号
setfacl -m g:hr:rw /tmp/acldemo.txt
[root@192 tmp]# ll
-rw-rw-r--+ 1 root root 0 1月  12 22:19 acldemo.txt
```

## 进程

### top

```shell
top - 21:08:58 up  6:15,  4 users,  load average: 0.01, 0.03, 0.05
Tasks: 262 total,   1 running, 261 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  1.4 sy,  0.0 ni, 98.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  3861300 total,  1922280 free,  1029068 used,   909952 buff/cache
KiB Swap:  2097148 total,  2097148 free,        0 used.  2545024 avail Mem
```

* 第一行

  * 程序名(top)

  * 21:08:58(系统时间)

  * up 6:15(运行时间)

  * 4 users(登录用户数)

  * load average(cpu 负载数,5分钟的平均负载,10分钟的平均负载,15分钟的平均负载)
* 第二行

  * task(总进程数)
  * 1 running (运行数:1)
  * 261 sleeping(睡眠树:1)
  * stopped(停止数)
  * zoimbie(僵尸数)
* 第三行

  * %cpu(cpu使用占比)
  * us (用户)
  * sy (系统)
  * ni(优先级)
  * id(空闲)
  * wa(等待)
  * hi(硬件)
  * st(虚拟机)
* 第四行

  * kib Mem (物理内存 单位:K)
  * free (空间,单位:k)
  * userd(使用)
  * cache(缓存硬盘内容大小)
* 第五行

  * 交换区
* 第六行
  * 进程ID
  * 用户名
  * 优先级(PR)
  * 内存(VIRT RES SHR)
  * 状态(S)
  * cpu
  * 内存
  * 运行时间
  * 命令

## 重定向管道

## 硬盘

* centos7
  * IDE并口
  * SATA串口
    * /dev/sda,sba:标识,s表示sata就是串口,d代表磁盘,a第一块
    * /dev/sdb

划分磁盘需要三个字母判断

划分分区需要4个文字判断,

1.

前面三个是一个磁盘

看后的 sda 来判断

再看后的数字,有1,2  这两个分区

2.sdb 以此类推,又是另外一些磁盘

3.或者是用 lsblk 命令

## 管理磁盘的三个步骤

先分区,再格式化,再挂载

![image-20210114203326197](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20210114203327.png)

MBR一共只能有4个分区,如果要需要很多个分区

![image-20210114202654364](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20210114202655.png)

![image-20210114202942146](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20210114202943.png)

### 新建主分区和逻辑分区

```shell
[root@192 ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   19G  0 part
  ├─centos-root 253:0    0   17G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0    5G  0 disk
sdc               8:32   0    5G  0 disk
sdd               8:48   0    5G  0 disk
sde               8:64   0    5G  0 disk
sdf               8:80   0    5G  0 disk
sdg               8:96   0    5G  0 disk
sdh               8:112  0    5G  0 disk
sdi               8:128  0    5G  0 disk
sr0              11:0    1  4.4G  0 rom

# 启动分区工具
[root@192 ~]# fdisk /dev/sdb
欢迎使用 fdisk (util-linux 2.23.2)。

更改将停留在内存中，直到您决定将更改写入磁盘。
使用写入命令前请三思。

Device does not contain a recognized partition table
使用磁盘标识符 0xda85d71c 创建新的 DOS 磁盘标签。

命令(输入 m 获取帮助)：n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
分区号 (1-4，默认 1)：
起始 扇区 (2048-10485759，默认为 2048)：+2G
Last 扇区, +扇区 or +size{K,M,G} (4194304-10485759，默认为 10485759)：
将使用默认值 10485759
分区 1 已设置为 Linux 类型，大小设为 3 GiB

命令(输入 m 获取帮助)：w
The partition table has been altered!

Calling ioctl() to re-read partition table.
正在同步磁盘。

# 刷新分区
[root@192 ~]# partprobe /dev/sdb


# 查看分区结果
[root@192 ~]# fdisk -l /dev/sdb

磁盘 /dev/sdb：5368 MB, 5368709120 字节，10485760 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0xda85d71c

   设备 Boot      Start         End      Blocks   Id  System
/dev/sdb1         4194304    10485759     3145728   83  Linux

# 格式化(创建文件系统)
# ext4 扩展文件系统第四代,是文件系统的类型
[root@192 ~]# mkfs.ext4 /dev/sdb1
mke2fs 1.42.9 (28-Dec-2013)
文件系统标签=
OS type: Linux
块大小=4096 (log=2)
分块大小=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
196608 inodes, 786432 blocks
39321 blocks (5.00%) reserved for the super user
第一个数据块=0
Maximum filesystem blocks=805306368
24 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912

Allocating group tables: 完成
正在写入inode表: 完成
Creating journal (16384 blocks): 完成
Writing superblocks and filesystem accounting information: 完成

# 手动挂载
[root@192 ~]# mkdir /mnt/disk1
[root@192 mnt]# mount -t ext4 /dev/sdb1 /mnt/disk1/

# 查看
[root@192 mnt]# df -hT
文件系统                类型      容量  已用  可用 已用% 挂载点
devtmpfs                devtmpfs  1.9G     0  1.9G    0% /dev
tmpfs                   tmpfs     1.9G     0  1.9G    0% /dev/shm
tmpfs                   tmpfs     1.9G   13M  1.9G    1% /run
tmpfs                   tmpfs     1.9G     0  1.9G    0% /sys/fs/cgroup
/dev/mapper/centos-root xfs        17G  4.4G   13G   26% /
/dev/sda1               xfs      1014M  239M  776M   24% /boot
tmpfs                   tmpfs     378M   12K  378M    1% /run/user/42
tmpfs                   tmpfs     378M     0  378M    0% /run/user/0
# 刚挂载的
/dev/sdb1               ext4      2.9G  9.0M  2.8G    1% /mnt/disk1
```

### 新建SWAP

```shell
[root@localhost ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   19G  0 part
  ├─centos-root 253:0    0   17G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0    5G  0 disk
└─sdb1            8:17   0    3G  0 part /mnt/disk1
sdc               8:32   0    5G  0 disk
sdd               8:48   0    5G  0 disk
sde               8:64   0    5G  0 disk
sdf               8:80   0    5G  0 disk
sdg               8:96   0    5G  0 disk
sdh               8:112  0    5G  0 disk
sdi               8:128  0    5G  0 disk
sr0              11:0    1  4.4G  0 rom  /run/media/anthony/CentOS 7 x86_64
[root@localhost ~]# fdisk /dev/sdb
欢迎使用 fdisk (util-linux 2.23.2)。

更改将停留在内存中，直到您决定将更改写入磁盘。
使用写入命令前请三思。


命令(输入 m 获取帮助)：n
Partition type:
   p   primary (2 primary, 0 extended, 2 free)
   e   extended
Select (default p): p
分区号 (3,4，默认 3)：
No free sectors available

命令(输入 m 获取帮助)：q

[root@localhost ~]# fdisk /dev/sdc
欢迎使用 fdisk (util-linux 2.23.2)。

更改将停留在内存中，直到您决定将更改写入磁盘。
使用写入命令前请三思。

Device does not contain a recognized partition table
使用磁盘标识符 0x661ba46a 创建新的 DOS 磁盘标签。

命令(输入 m 获取帮助)：n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
分区号 (1-4，默认 1)：
起始 扇区 (2048-10485759，默认为 2048)：
将使用默认值 2048
Last 扇区, +扇区 or +size{K,M,G} (2048-10485759，默认为 10485759)：
将使用默认值 10485759
分区 1 已设置为 Linux 类型，大小设为 5 GiB

命令(输入 m 获取帮助)：p

磁盘 /dev/sdc：5368 MB, 5368709120 字节，10485760 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x661ba46a

   设备 Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

命令(输入 m 获取帮助)：w
The partition table has been altered!

Calling ioctl() to re-read partition table.
正在同步磁盘。
[root@localhost ~]# partprobe /dev/sdc
[root@localhost ~]# fdisk -l /dev/sdb

磁盘 /dev/sdb：5368 MB, 5368709120 字节，10485760 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0xda85d71c

   设备 Boot      Start         End      Blocks   Id  System
/dev/sdb1         4194304    10485759     3145728   83  Linux
/dev/sdb2            2048     4194303     2096128   83  Linux

Partition table entries are not in disk order
[root@localhost ~]# mkswap /dev/sdb2
/dev/sdb2: 没有那个文件或目录
[root@localhost ~]# mkswap /dev/sdc2
/dev/sdc2: 没有那个文件或目录
[root@localhost ~]# fdisk -l /dev/sdc

磁盘 /dev/sdc：5368 MB, 5368709120 字节，10485760 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x661ba46a

   设备 Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux
[root@localhost ~]# mkswap /dev/sdc1
anaconda-ks.cfg       .bashrc               .dbus/                .local/               模板/                 下载/
.bash_history         .cache/               .esd_auth             .tcshrc               视频/                 音乐/
.bash_logout          .config/              .ICEauthority         .viminfo              图片/                 桌面/
.bash_profile         .cshrc                initial-setup-ks.cfg  公共/                 文档/
[root@localhost ~]# mkswap /dev/sdc1
正在设置交换空间版本 1，大小 = 5241852 KiB
无标签，UUID=074198c5-dff9-4834-9fbb-06cee839372d
[root@localhost ~]# df -hT
文件系统                类型      容量  已用  可用 已用% 挂载点
devtmpfs                devtmpfs  1.9G     0  1.9G    0% /dev
tmpfs                   tmpfs     1.9G     0  1.9G    0% /dev/shm
tmpfs                   tmpfs     1.9G   13M  1.9G    1% /run
tmpfs                   tmpfs     1.9G     0  1.9G    0% /sys/fs/cgroup
/dev/mapper/centos-root xfs        17G  4.4G   13G   26% /
/dev/sda1               xfs      1014M  239M  776M   24% /boot
/dev/sdb1               ext4      2.9G  9.0M  2.8G    1% /mnt/disk1
tmpfs                   tmpfs     378M   32K  378M    1% /run/user/1000
/dev/sr0                iso9660   4.4G  4.4G     0  100% /run/media/anthony/CentOS 7 x86_64
tmpfs                   tmpfs     378M     0  378M    0% /run/user/0
[root@localhost ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   19G  0 part
  ├─centos-root 253:0    0   17G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0    5G  0 disk
└─sdb1            8:17   0    3G  0 part /mnt/disk1
sdc               8:32   0    5G  0 disk
└─sdc1            8:33   0    5G  0 part
sdd               8:48   0    5G  0 disk
sde               8:64   0    5G  0 disk
sdf               8:80   0    5G  0 disk
sdg               8:96   0    5G  0 disk
sdh               8:112  0    5G  0 disk
sdi               8:128  0    5G  0 disk
sr0              11:0    1  4.4G  0 rom  /run/media/anthony/CentOS 7 x86_64
[root@localhost ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           3770         919        2208          26         642        2586
Swap:          2047           0        2047
[root@localhost ~]# swapon /dev/sdc1
[root@localhost ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           3770         925        2201          26         642        2579
Swap:          7166           0        7166
```

## 创建LVM

无限制的扩张容量

```shell
[root@192 ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   19G  0 part
  ├─centos-root 253:0    0   17G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0    5G  0 disk
├─sdb1            8:17   0    3G  0 part
└─sdb2            8:18   0    2G  0 part
sdc               8:32   0    5G  0 disk
└─sdc1            8:33   0    5G  0 part
# 一会要操作的是这个盘
sdd               8:48   0    5G  0 disk
sde               8:64   0    5G  0 disk
# 一会要操作的是这个盘
sdf               8:80   0    5G  0 disk
sdg               8:96   0    5G  0 disk
sdh               8:112  0    5G  0 disk
sdi               8:128  0    5G  0 disk


# 物理磁盘转成物理卷
[root@192 ~]# pvcreate /dev/sdd
  Physical volume "/dev/sdd" successfully created.

# 创建数据卷组
[root@192 ~]# vgcreate vg1 /dev/sdd
  Volume group "vg1" successfully created

# 创建逻辑卷
# lvcreate
# -L 4g,要用磁盘的4G
# -n lv1 卷的名字,自定义
# vg1 卷组名,要把4G,加入到哪个卷组
[root@192 ~]# lvcreate -L 4G -n lv1 vg1
  Logical volume "lv1" created.

# 格式化
# 这里要用/dev/卷组名/逻辑卷的名字
[root@192 ~]# mkfs.ext4 /dev/vg1/lv1
mke2fs 1.42.9 (28-Dec-2013)
文件系统标签=
OS type: Linux
块大小=4096 (log=2)
分块大小=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
262144 inodes, 1048576 blocks
52428 blocks (5.00%) reserved for the super user
第一个数据块=0
Maximum filesystem blocks=1073741824
32 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: 完成
正在写入inode表: 完成
Creating journal (32768 blocks): 完成
Writing superblocks and filesystem accounting information: 完成

# 挂载,这里要用/dev/卷组名/逻辑卷的名字
[root@192 ~]# mkdir /mnt/lv1
[root@192 ~]# mount /dev/vg1/lv1  /mnt/lv1

[root@192 ~]# df -hT
文件系统                类型      容量  已用  可用 已用% 挂载点
devtmpfs                devtmpfs  1.9G     0  1.9G    0% /dev
tmpfs                   tmpfs     1.9G     0  1.9G    0% /dev/shm
tmpfs                   tmpfs     1.9G   13M  1.9G    1% /run
tmpfs                   tmpfs     1.9G     0  1.9G    0% /sys/fs/cgroup
/dev/mapper/centos-root xfs        17G  4.4G   13G   26% /
/dev/sda1               xfs      1014M  239M  776M   24% /boot
tmpfs                   tmpfs     378M   28K  378M    1% /run/user/1000
/dev/sr0                iso9660   4.4G  4.4G     0  100% /run/media/anthony/CentOS 7 x86_64
# 这里就有4个G的可用空间
/dev/mapper/vg1-lv1     ext4      3.9G   16M  3.6G    1% /mnt/lv1

# 把sdf物理磁盘转成物理卷
[root@192 ~]# pvcreate /dev/sdf
  Physical volume "/dev/sdf" successfully created.

[root@192 ~]# vgextend vg1 /dev/sdf
  Volume group "vg1" successfully extended

# 扩容lv
[root@192 ~]# lvextend -L +4G /dev/vg1/lv1
  Size of logical volume vg1/lv1 changed from 4.00 GiB (1024 extents) to 8.00 GiB (2048 extents).
  Logical volume vg1/lv1 successfully resized.

# 刷新
[root@192 ~]# resize2fs /dev/vg1/lv1
resize2fs 1.42.9 (28-Dec-2013)
Filesystem at /dev/vg1/lv1 is mounted on /mnt/lv1; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 1
The filesystem on /dev/vg1/lv1 is now 2097152 blocks long.

# 再次查看
[root@192 ~]# df -hT
文件系统                类型      容量  已用  可用 已用% 挂载点
devtmpfs                devtmpfs  1.9G     0  1.9G    0% /dev
tmpfs                   tmpfs     1.9G     0  1.9G    0% /dev/shm
tmpfs                   tmpfs     1.9G   13M  1.9G    1% /run
tmpfs                   tmpfs     1.9G     0  1.9G    0% /sys/fs/cgroup
/dev/mapper/centos-root xfs        17G  4.4G   13G   26% /
/dev/sda1               xfs      1014M  239M  776M   24% /boot
tmpfs                   tmpfs     378M   28K  378M    1% /run/user/1000
# 这里就有8个G的可用空间
/dev/mapper/vg1-lv1     ext4      7.8G   18M  7.4G    1% /mnt/lv1

[root@192 ~]# pvs
  PV         VG     Fmt  Attr PSize   PFree
  /dev/sda2  centos lvm2 a--  <19.00g    0
  /dev/sdd   vg1    lvm2 a--   <5.00g    0
  /dev/sdf   vg1    lvm2 a--   <5.00g 1.99g

[root@192 ~]# vgs
  VG     #PV #LV #SN Attr   VSize   VFree
  centos   1   2   0 wz--n- <19.00g    0
  vg1      2   1   0 wz--n-   9.99g 1.99g
```

## yum

yum的所有文件都在`/etc/yum.repos.d`下

## yum挂载光盘源

新建一个xxx.repo的文件

```shell
[root@192 yum.repos.d]# cat ./dvd.repo
[dvd]
name=this is descreption
baseurl=file:///mnt/cdrom
gpgcheck=0
```

挂载光盘

```shell
[root@192 yum.repos.d]# df -hT
文件系统                类型      容量  已用  可用 已用% 挂载点
devtmpfs                devtmpfs  1.9G     0  1.9G    0% /dev
tmpfs                   tmpfs     1.9G     0  1.9G    0% /dev/shm
tmpfs                   tmpfs     1.9G   13M  1.9G    1% /run
tmpfs                   tmpfs     1.9G     0  1.9G    0% /sys/fs/cgroup
/dev/mapper/centos-root xfs        17G  4.4G   13G   26% /
/dev/sda1               xfs      1014M  239M  776M   24% /boot
tmpfs                   tmpfs     378M   32K  378M    1% /run/user/1000
/dev/mapper/vg1-lv1     ext4      7.8G   18M  7.4G    1% /mnt/lv1
/dev/sr0                iso9660   4.4G  4.4G     0  100% 

# 挂载
mount /dev/sr0 /mnt/cdrom

# 查看光盘你的包
ls /mnt/cdrom/Packages/

# 安装软件
yum -y instal httpd

# 安装rpm包
rpm -ivh  xxx.rpm
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

