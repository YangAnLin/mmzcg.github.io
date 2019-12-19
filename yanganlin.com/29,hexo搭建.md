

### 一:准备
> hexo官网:https://hexo.io/  
> git官网:https://git-scm.com/  
> github官网:https://github.com/  
> node.js官网:https://nodejs.org/en/  
> 买个域名

### 二:安装
先安装好`git`,和`node.js`
```shell
BugYang@DESKTOP-QPRK3K4 MINGW64 ~/Desktop/myhexoback (master)
$ git --version
git version 2.16.1.windows.2

BugYang@DESKTOP-QPRK3K4 MINGW64 ~/Desktop/myhexoback (master)
$ node -v
v9.11.1
```

新建个文件夹(里面到时候就会装hexo的源码),这里我起个myhexo这个名字
`cd myhexo`

hexo安装(参考官网)

```
npm install hexo-cli -g  ==>安装客户端
hexo init     ==>初始化hexo
npm install 
hexo server   ==>启动服务
```

现在用浏览器访问：http://localhost:4000/
![image](http://ociu0xfjm.bkt.clouddn.com/18-4-7/6883366.jpg)

官网有个步骤是:`hexo init blog`,运行了这个之后,会再myhexo里再创建一个文件夹叫blog,然后hexo的源码就会blog文件夹里面

安装好之后,差不多就这些文件,因为我的博客已经运行起来了,所以才会有这么多,如果你的文件少了,没有关系
```
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----         2018/4/7     11:29                node_modules
d-----         2018/4/7     14:35                public
d-----         2018/4/6     13:34                scaffolds
d-----         2018/4/7      4:15                source
d-----         2018/4/7     16:36                themes
-a----         2018/4/7      1:42             81 .gitignore
-a----         2018/4/7     16:07         278378 db.json
-a----         2018/4/6      7:29         166964 package-lock.json
-a----         2018/4/6      7:00            770 package.json
-a----         2018/4/7     11:47           1916 _config.yml
```

## 三,把项目部署到Github上
### 1.在github创建一个项目,格式就是 `你的github名字.github.io`
![](http://ociu0xfjm.bkt.clouddn.com/18-4-7/11923022.jpg)

### 2.生成github秘钥(`ssh-keygen` 命令)
```
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/schacon/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/schacon/.ssh/id_rsa.
Your public key has been saved in /Users/schacon/.ssh/id_rsa.pub.
The key fingerprint is:
43:c5:5b:5f:b1:f1:50:43:ad:20:a6:92:6a:1f:9a:3a schacon@agadorlaptop.local
```
生成的时候,除了需要输`yes`,其余的都按空格

### 3.查看公钥(`cat ~/.ssh/id_rsa.pub` 命令)
```
$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAklOUpkDHrfHY17SbrmTIpNLTGK9Tjom/BWDSU
GPl+nafzlHDTYW7hdI4yZ5ew18JH4JW9jbhUFrviQzM7xlELEVf4h9lFX5QVkbPppSwg0cda3
Pbv7kOdJ/MTyBlWXFCR+HAo3FXRitBqxiX1nKhXpHAZsMciLq8V6RjsNAQwdsdMFvSlVK/7XA
t3FaoJoAsncM1Q9x5+3V0Ww68/eIFmb1zuUFljQJKprrX88XypNDvjYNby6vw/Pb0rwert/En
mZ+AW4OZPnTPI89ZPmVMLuayrD2cE86Z/il8b+gw3r3+1nKatmIkjn2so1d01QraTlMqVSsbx
NrRFi9wrf+M7Q== schacon@agadorlaptop.local
```

### 4.把公钥添加到github
![image](http://ociu0xfjm.bkt.clouddn.com/18-4-7/19398755.jpg)

### 5.然后进入myhexo文件夹,里面有个`_config.yml`文件,注意这里是在根目录,根目录,根目录,重要的事情说三遍
```
# Deployment
## 这里是重点，这里是修改发布地址，因为我们前面已经加了 SSH 密钥信息在 Github 设置里面了，所以只要我们电脑里面持有那两个密钥文件就可以无需密码地跟 Github 做同步。
## 需要注意的是这里的 repo 采用的是 ssh 的地址，而不是 https 的。分支我们默认采用 master 分支，以后你翅膀硬了要换其他也无所谓。
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.com:judasn/judasn.github.io.git
  branch: master
```
替换你自己项目的git地址.  
**需要特别提醒： YMAL 文件有严格的格式要求，多一个空格，少一个空格 都有可能出问题。所以请严格按照模板中的格式来**

### 6.开始部署了
```
npm install hexo -server --save          #安装插件
npm install hexo-deployer-git --save     #安装插件

hexo clean #清理
hexo g  #生成
hexo d  #部署到github
```

### 7.在浏览器里输入你的仓库名就是`你的github名字.github.io`

## 四:更换主题
在这里找你喜欢的主题`https://hexo.io/themes/`  
以这个`maupassant`为例  
maupassant的文档:`https://www.haomwei.com/technology/maupassant-hexo.html`
**都是在myhexo的根目录操作的**
```
$ git clone https://github.com/tufu9441/maupassant-hexo.git themes/maupassant
$ npm install hexo-renderer-pug --save
$ npm install hexo-renderer-sass --save
```

修改myhexo根目录下的`_config.yml`,将theme的值改为maupassant。记得有空格

`git clone` 完之后,你会看到myhexo的根目录的themes 下多了一个maupassant文件,而且这个文件夹下还多了一个`_config.yml`文件,所以通常myhexo文件夹下有两个`_config.yml`,根目录的那个,是全局配置文件,而主题下的`_config.yml`是用来配置主题的,

然后再
```
hexo clean
hexo g
hexo d
```
过几秒钟,再去浏览器里输入你仓库的名字看看,

或者是在`hexo g`完之后再输入`hexo s`,运行本地服务,localhost:4000 就可以立马看到效果了

## 五:绑定域名
创建一个CNAME的文件,不需要后缀,放在`myhexo\source` 目录下
里面就写你的域名,不要`www`

除了红框的地址写仓库名字,其余的都写一样的
![](http://ociu0xfjm.bkt.clouddn.com/18-4-7/37348800.jpg)

再
```
hexo clean
hexo g
hexo d
```
过一会,去访问看看


> 参考YouMeek:http://www.youmeek.com/hexo-install/