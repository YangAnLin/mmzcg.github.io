## Win的一些操作
### 1.杀线程
```shell
# 查找5091的端口
netstat -aon | find "5091"
# 杀5091的PID
taskkill /f /pid 6880
```