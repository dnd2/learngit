## NodeJs Notebook

~~~javascript
const http = require('http');

const hostname = '127.0.0.1';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(\`Server running at http://${hostname}:${port}/\`);
});
~~~

Note: 如果在createServer的匿名函数中加入一段输出语句，当我们在服务器访问网页时，我们的服务器可能会输出两次“Request received.”。那是因为大部分服务器都会在你访问 http://localhost:8888 /时尝试读取 http://localhost:8888/favicon.ico

**exports** 

Node.js模块 require和 exports
http://www.cnblogs.com/pigtail/archive/2013/01/14/2859555.html
https://liuzhichao.com/p/1669.html

###Nodejs 调试###

使用nodes内置debug模块

- node debug index.js (需要在代码中加入debugger来设置断点，或通过setBrekpoint('index.js', 3)指定)

调试代码存在2种状态：
	
	1. 操作调试位置，如进入下一步，进入函数，跳出函数，此时为debug模式
	2. 查看变量值，如果查看循环计数器i的值，此时为real状态  （[REPL](https://segmentfault.com/a/1190000002673137)
	
**使用node-inspector进行debug**

npm install -g node-inspector

node-inspector --web-port=8080 --debug-port=5858  # 启动node inspector

* node —debug index.js   会执行完所有代码，一般在监听事件时使用
* node —debug-brk index.js   会等待外部调用接入后进入代码，一般是从第一行开始

(sudo lsof -i:5858  查找当前占用端口号)

**webstorm中添加debug配置**

* 添加node配置  

![](http://www.barretlee.com/blogimgs/2015/10/20151003_6988a758.jpg)

* 配置执行项

![](http://www.barretlee.com/blogimgs/2015/10/20151003_52fb09e8.jpg)

* Node interpreter 是你 node 程序的位置
* Node parameters 是开启 nodejs 程序的选项，如果使用了 ES6 特性，需要开始 --harmony 模式，如果需要远程调试程序，可以使用 --debug 命令，我们采用控制台调试，显然是不需要添加 --debug 参数的。
* Working directory 是文件的目录
* Javascript file 是需要调试的文件

[参考原文](http://www.barretlee.com/blog/2015/10/07/debug-nodejs-in-command-line/)

后期补充: 

  * debug中的技巧
  * 远程debug
  * 性能分析

