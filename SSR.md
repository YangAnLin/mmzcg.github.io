安装

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

卸载

```shell
shadowsocks-all.sh uninstall
```

