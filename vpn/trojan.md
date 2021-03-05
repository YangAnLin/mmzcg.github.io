```shell
# pem转crt
openssl x509 -in fullchain.pem -out fullchain.crt  

# 完整编译trojan-go
go build -tags "full"

# 生成证书
# 下载acme
curl  https://get.acme.sh | sh
# 设置别名
alias acme.sh=~/.acme.sh/acme.sh
# 有两种方法
# 1.HTTP
# 1.安装软件,socat启动一个80端口的临时服务器
apt install socat
acme.sh --issue -d airports.ink --standalone
cd /root/.acme/airports.ink/
# 2.DNS
```

```shell

    5  apt install unzip
    2  wget https://github.com/p4gefau1t/trojan-go/releases/download/v0.8.2/trojan-go-linux-amd64.zip
    4  unzip trojan-go-linux-amd64.zip -d /etc/trojan-go
    7  cd /etc/trojan-go/
    17  cp /etc/trojan-go/example/server.json /etc/trojan-go
   18  cp /etc/trojan-go/example/trojan-go.service /etc/systemd/system
   22  ./trojan-go -config ./server.json 
   20  vim ./server.json 
   47  acme.sh  --issue -d airports.ink   --standalone
       53  cp airports.ink.cer /etc/trojan-go/airports.ink.cer
   54  cp airports.ink.key /etc/trojan-go/airports.ink.key
   60  ./trojan-go -api-addr 127.0.0.1:10000 -api list
```

