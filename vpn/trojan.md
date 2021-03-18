```shell
# pem转crt
openssl x509 -in fullchain.pem -out fullchain.crt  

# 完整编译trojan-go
go build -tags "full"
```

```shell
# 下载安装
# 下载解压工具
apt install unzip
# 下载安装包
wget https://github.com/p4gefau1t/trojan-go/releases/download/v0.8.2/trojan-go-linux-amd64.zip
# 解压安装包
unzip trojan-go-linux-amd64.zip -d /etc/trojan-go
# 移动启动配置文件
mv /etc/trojan-go/example/server.json /etc/trojan-go
# 移动开机自启动配置文件
mv /etc/trojan-go/example/trojan-go.service /etc/systemd/system

# 在DNS添加一条记录:IP指向域名
# cloudfare需要用DNS解析

# 安装证书
# 安装acme.sh
curl https://get.acme.sh | sh
# 生效acme.sh
source ~/.bashrc
# 假装80端口的网站,Http认证
apt install socat
acme.sh --issue -d airportstech.xyz --standalone
# 迁移证书位置
cp /root/.acme.sh/域名/域名.cer /etc/trojan-go/airports.ink.cer
cp /root/.acme.sh/域名/域名.key /etc/trojan-go/airports.ink.key

# 编辑trojan-go配置文件
vim /etc/trojan-go/server.json
{
    "run_type": "server",
    "local_addr": "0.0.0.0",
    "local_port": 443,
    "remote_addr": "blog.airports.ink",
    "remote_port": 443,
    "password": [
        "123456"
    ],
    "ssl": {
        "cert": "airportstech.xyz.cer",
        "key": "airportstech.xyz.key",
        "sni": "airportstech.xyz"
    },
    "router": {
        "enabled": true,
        "block": [
            "geoip:private"
        ],
        "geoip": "/etc/trojan-go/geoip.dat",
        "geosite": "/etc/trojan-go/geosite.dat"
    },
    "api": {
        "enabled": true,
        "api_addr": "127.0.0.1",
        "api_port": 10000
        }

}

# 启动
/etc/trojan-go/trojan-go -config /etc/trojan-go/server.json
```

win10 客户端

```shell
https://github.com/kidonng/sushi/releases/download/binaries/Trojan-Qt5-Windows-1.4.0.7z
```

