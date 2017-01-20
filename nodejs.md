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