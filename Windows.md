## Win10杀线程
```shell
# 查找5091的端口
netstat -aon | find "5091"
# 杀5091的PID
taskkill /f /pid 6880
```


## Win10安装Scoop包管理器
Scoop是windows的包管理器

```shell
# 使用了PowerShell在你当前Windows的账户下
set-executionpolicy remotesigned -s cu

# 在PowerShell下输入
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
```

## Win10安装concfg,用来清空掉命令行和powershell的多配置文件
github仓库:`https://github.com/lukesampson/concfg`
```shell
# 安装concfg软件
scoop install concfg

# 删除掉多余的配置文件
concfg clean

# 导入内置的主题配置,还有别的配置
concfg import solarized-dark
```

## Win10 Cmd和Powershell的默认值和属性的区别
默认值和属性的不同设置,从哪里启动有关系,比如用开始菜单的 和 从shift + 右键,加载的命令行不一样,还是安装好concfg软件,把多余的都删掉,只留一个

如果你打开 PowerShell 快捷方式时不小心右键修改它的“属性”中的设置项的话，那么它的表现又会不同了。所以如果想一直保持一致，最好不要动任何一个控制台实例的“属性”。
`https://www.zhihu.com/question/63867578/answer/220101109`

## Powershell美化教程
用的是这三个地址,等下回安装的时候再写的详细点
```
https://zhuanlan.zhihu.com/p/51901035
https://blog.walterlv.com/post/beautify-powershell-like-zsh.html
https://coolcode.org/2018/03/16/how-to-make-your-powershell-beautiful/
```

## Docker和VMware的虚拟机冲突,把Windows自带的删除掉
![](https://blog-anthony.s3-ap-northeast-1.amazonaws.com/blog/copy_20201226164302.png)

再用管理员的powershell执行
`bcdedit /set hypervisorlaunchtype off`



## 把软件加入到自启动

在运行里输入`shell:startup`



## WSL2安装

1.开启

![](https://blog-anthony.s3-ap-northeast-1.amazonaws.com/blog/copy_20201226164244.png)

2.下载安装镜像

![](https://blog-anthony.s3-ap-northeast-1.amazonaws.com/blog/copy_20201226164111.png)

![](https://blog-anthony.s3-ap-northeast-1.amazonaws.com/blog/copy_20201226164126.png)

3.设置wsl2

![](https://blog-anthony.s3-ap-northeast-1.amazonaws.com/blog/copy_20201226164053.png)

```shell
PS C:\Users\anthony> wsl -l -v
  NAME            STATE           VERSION
* Ubuntu-20.04    Stopped         2
```



## EverythingToolbar

`https://github.com/stnkl/EverythingToolbar`

## Vscode 安装图片上传插件

1.在插件那搜索picgo,安装好插件在插件那里点设置
2.配置阿里云oss,参考:`https://picgo.github.io/PicGo-Doc/`

```json
{
  "accessKeyId": "",
  "accessKeySecret": "",
  "bucket": "", // 存储空间名
  "area": "", // 存储区域代号
  "path": "", // 自定义存储路径
  "customUrl": "" // 自定义域名，注意要加http://或者https://
}
```

3.通过三个组合键，可以分别从：1.（Ctrl+alt+U）剪切板 2.（Ctrl+alt+E）文件夹 3.（Ctrl+alt+O）指定路径

## PicGO+Github

生成token:`在github->setting->developer settings 选择generate new token`

![img](https://blog-anthony.s3-ap-northeast-1.amazonaws.com/blog/20190620171238.png)

`记得保存好密码,只会创建成功后显示,以后就看不到了`

PicGo设置

```shell
仓库名:YangAnLin/images
分支名:master
设定token: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
设定自定义域名:https://cdn.jsdelivr.net/gh/YangAnLin/images
```

# PicGO+Aws+S3

> 参考:
>
> [S3教程](https://atsud0.me/2021/02/24/%E4%BD%BF%E7%94%A8Amazon-S3%E5%AE%9E%E7%8E%B0%E5%9B%BE%E5%BA%8A/)
>
> [PicGoS3教程](https://github.com/wayjam/picgo-plugin-s3)

### 创建存储桶

红框里不要勾选

![img](https://blog-anthony.s3.ap-northeast-1.amazonaws.com/blog/2021/29ee1bcebc15aebc50a14362a56bdef9.jpeg)

### 创建文件夹,设置公开

![img](https://blog-anthony.s3.ap-northeast-1.amazonaws.com/blog/2021/48842ec56642aa11212974b917ddba6f.jpeg)

### 开启静态托管

![img](https://blog-anthony.s3.ap-northeast-1.amazonaws.com/blog/2021/abe6b1a8b24ffb8852499dd45a8c9644.png)

### 新建IAM用户

访问创建IAM页面

![image-20210429151642078](https://blog-anthony.s3-ap-northeast-1.amazonaws.com/blog/2021/077b1e44f1381d03e0c9a82a31892edf.png)

创建使用者

![img](https://blog-anthony.s3.ap-northeast-1.amazonaws.com/blog/2021/13d91eb81e7e0acb023e9bae0c30a61b.jpeg)

创建使用者详情

![img](https://blog-anthony.s3.ap-northeast-1.amazonaws.com/blog/2021/1f21a24f3ee1a745ecfd6103a6a6fd73.png)

创建使用者权限,点击下一步,然后就能看到key和密钥了

![img](https://blog-anthony.s3.ap-northeast-1.amazonaws.com/blog/2021/518fb468c4dbb38aefb0679fb88d1193.jpeg)

PicGO软件许要下再S3插件,然后再配置文件里粘贴这个


```json
"aws-s3": {
    "accessKeyID": "AKIA2S37TMJ5N6PCZPM6",
    "secretAccessKey": "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",
    "bucketName": "blog-anthony",
    "uploadPath": "blog/{year}/{md5}.{extName}",
    "acl": "public-read"
}
```

