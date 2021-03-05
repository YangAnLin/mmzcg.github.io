# SS安装

```shell
# 下载脚本文件
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh

# 给权限
chmod +x shadowsocks-all.sh

# 运行
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log

# 安装成功
Congratulations, Shadowsocks-libev server install completed!
Your Server IP        :  149.248.15.200
Your Server Port      :  12631
Your Password         :  123456
Your Encryption Method:  aes-256-gcm
# ss协议地址
Your QR Code: (For Shadowsocks Windows, OSX, Android and iOS clients)
 ss://YWVzLTI1Ni1nY206MTIzNDU2QDE0OS4yNDguMTUuMjAwOjEyNjMx
# 图片扫描连接
Your QR Code has been saved as a PNG file path:
 /root/shadowsocks_libev_qr.png

# https://teddysun.com/ 安装的时候自定义
Welcome to visit: https://teddysun.com/486.html
Enjoy it!
```

SS卸载

```shell
shadowsocks-all.sh uninstall
```

SSM安装

配置文件

`vim ~/.ssmgr/default.yml`

```yaml
type: s
shadowsocks:
  address: 127.0.0.1:6001
manager:
  address: 0.0.0.0:6002
  password: '123456'
db: 'db.sqlite'
```

`vim ~/.ssmgr/webgui.yml`

```yaml
type: m
manager:
  address: 127.0.0.1:6002
  password: '123456'
plugins:
  flowSaver:
    use: true
  user:
    use: true
  account:
    use: true
  email:
    use: true
    type: 'smtp'
    username: 'username'
    password: 'password'
    host: 'smtp.your-email.com'
  webgui:
    use: true
    host: '0.0.0.0'
    port: '80'
    site: '67.43.212.2'
    # admin_username: 'youremail@address.com'
    # admin_password: '35710935109364'
    # icon: 'icon.png'
    # skin: 'default'
    # language: 'en-US'
    # googleAnalytics: 'UA-xxxxxxxx-x'
    # gcmSenderId: '476902381496'
    # gcmAPIKey: 'AAAAGzddLRc:XXXXXXXXXXXXXX'
    # google_login_client_id: '724695589056-p78tu8738t4fjel56yhe34qq34gjufsi.apps.googleusercontent.com'
    # google_login_client_secret: 'TjUd36YnQ-YUI2uUtQa_43Tl'
    # facebook_login_client_id: '9825686749820123'
    # facebook_login_client_secret: 'a46c6bb6f8281c23d2b74b43008c9c46'
    # github_login_client_id: '7c45c34c1de3ef937d37'
    # github_login_client_secret: 'd2768efe5258cfb9ce4da11ed7ddc334bc65756b'
    # twitter_login_consumer_key: 'tKPH3RViDT68PtHBMHYJuQ'
    # twitter_login_consumer_secret: 'wYCtWdUSEfm8H3ES0r5rgHKeqGvYGiFDrGj4THiq3T6'

db: 'webgui.sqlite'
redis:
  host: 'your.redis.host'
  port: 6379
  password: 'yourRedisPassword'
  db: 0
  
  
# 启动ss的管理节点
ss-manager -m aes-256-gcm -u --manager-address 127.0.0.1:6001
# 或者
# -v 啰嗦模式
# -m 加密方式,但是还不知道这里有什么用,写不写都没有影响api请求的
ss-manager --manager-address 127.0.0.1:6001 -v

# 启动ssmgr接收控制的程序
/shadowsocks-manager/bin/ssmgr -c ~/.ssmgr/default.yml
```



```shell
sudo su -

# 下载源码
git clone https://github.com/shadowsocks/shadowsocks-manager.git

# 进入目录
cd shadowsocks-manager

# 打包运行
npm i
npm run build
/shadowsocks-manager/bin/ssmgr -c ~/.ssmgr/webui.yml
```

# 扩展



# 账号

| 名字      | 地址                        | 账号密码                                 |
| --------- | --------------------------- | ---------------------------------------- |
| 站点      | https://airports.ink        |                                          |
| 后台站点  | https://admin.airports.ink  |                                          |
| 邮箱      | https://mail.protonmail.com | 账号密码:airports.ink  / Lee072224820522 |
| wordpress | blog.airports.ink           | 账号密码:admin  /  Lee07224820522        |

# 转载

[Python3 UNIX domain sockets使用代码实现](https://www.cnblogs.com/lsdb/p/12191095.html)

[ss-bash](https://github.com/hellofwy/ss-bash/wiki)



## SS源码

```解密
# 源码在这里
https://github.com/golang/crypto
```