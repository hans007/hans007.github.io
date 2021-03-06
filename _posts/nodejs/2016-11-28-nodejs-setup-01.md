---
layout: post
title: nodejs - 第一节:安装并运行helloword!
category: nodejs
tags:
  - nodejs
---

# 安装

> https://nodejs.org/en/download/
> 建议用安装包


- 命令行方式

```

环境
yum install -y gcc-c++ make
curl --silent --location https://rpm.nodesource.com/setup_6.x | bash -

老版本先删除
yum remove -y nodejs npm

安装
yum -y install nodejs
```

- 如果安装冲突 建议删除安装

```
yum remove nodejs npm -y

- 清理目录
/usr/local/lib
/usr/local/include
```

- 升级

```
sudo npm cache clean -f
sudo npm install -g n
sudo n 6.11.00
or
sudo n stable
```

# 测试环境 helloword

- 新建文件 helloword.js

```javascript
console.log("helloword!");
```

- 运行

```
$ node helloword.js
```

- 输出

```
helloword
```

# 创建一个 web server

- 新建文件 webserver.js

```javascript
var http = require("http")
http.createServer(function(request, response){
  response.writeHead(200,{'content-Type':'text/plain'});
  response.end('hello word!');
}).listen(8888);

console.log('web server is running! http://127.0.0.1:8888/');
```

> `require` 调用 `http` 模块
> 服务`listen`监听在 8888 端口

- 运行

```
$ node webserver.js
```

- 输出

浏览器输入 http://127.0.0.1:8888/

# 参考

> https://nodejs.org/en/docs/guides/anatomy-of-an-http-transaction/

# 代码

> https://github.com/hans007/JavaScriptCodes/tree/master/nodejs-do

[我的博客](https://hans007.github.io)