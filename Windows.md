## Win的一些操作
### 1.杀线程
```shell
# 查找5091的端口
netstat -aon | find "5091"
# 杀5091的PID
taskkill /f /pid 6880
```


# 安装scoop包管理器,是windows的包管理器

```shell
# 使用了PowerShell在你当前Windows的账户下
set-executionpolicy remotesigned -s cu

# 在PowerShell下输入
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
```

# 安装concfg,用来清空掉命令行和powershell的多配置文件
github仓库:`https://github.com/lukesampson/concfg`
```shell
# 安装concfg软件
scoop install concfg

# 删除掉多余的配置文件
concfg clean

# 导入内置的主题配置,还有别的配置
concfg import solarized-dark
```

# cmd和powershell的默认值和属性的区别
默认值和属性的不同设置,从哪里启动有关系,比如用开始菜单的 和 从shift + 右键,加载的命令行不一样,还是安装好concfg软件,把多余的都删掉,只留一个

如果你打开 PowerShell 快捷方式时不小心右键修改它的“属性”中的设置项的话，那么它的表现又会不同了。所以如果想一直保持一致，最好不要动任何一个控制台实例的“属性”。
`https://www.zhihu.com/question/63867578/answer/220101109`


# Vscode 安装图片上传插件
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

# 美化powershell教程
用的是这三个地址,等下回安装的时候再写的详细点
```
https://zhuanlan.zhihu.com/p/51901035
https://blog.walterlv.com/post/beautify-powershell-like-zsh.html
https://coolcode.org/2018/03/16/how-to-make-your-powershell-beautiful/
```