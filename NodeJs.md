npm和node.js更换版本

```shell
# npm更换
npm install npm@6.10.1 -g 

# node更换
sudo npm install n -g
sudo n 版本号
n # 选择版本号
```

node.js升级

```shell
# 更新npm
npm install -g npm

# 清空npm缓存
npm cache clean -f

# 安装n模块
npm install -g n

# 升级node.js到最新稳定版
n stable

# 有时候需要sudo权限,或者安装好了之后要开启一个新的shell才能用到新的版本
```

