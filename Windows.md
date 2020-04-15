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


