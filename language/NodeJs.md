# 入门应用

```js
var http = require('http');

http.createServer(function (request, response) {

    // 发送 HTTP 头部 
    // HTTP 状态值: 200 : OK
    // 内容类型: text/plain
    response.writeHead(200, {'Content-Type': 'text/plain'});

    // 发送响应数据 "Hello World"
    response.end('Hello World\n');
}).listen(8888);

// 终端打印如下信息
console.log('Server running at http://127.0.0.1:8888/');


// 运行
node server.js
```

Npm升级

```shell
npm i -g npm to update
```

# Node.js

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

https://www.bilibili.com/video/BV1vE411871g?p=120

[安装源](https://github.com/nodesource/distributions#debinstall)

  # ES6 & JS

[字符串转数字](https://blog.fundebug.com/2018/07/07/string-to-number/)

[遍历2](https://segmentfault.com/a/1190000010203337)

[排序](https://segmentfault.com/a/1190000015961859)

[排序](https://blog.csdn.net/weixin_46146313/article/details/104198636)

[join方法](https://www.w3school.com.cn/jsref/jsref_join.asp)

遍历

```js
  JavaScript for...in ..
```

foreach

```javascript
row.typeID.split(",").forEach(id=>{this.gameTypes.push(parseInt(id))})
```