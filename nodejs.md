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

###exports & module.exports

* exports是module.exports的辅助方法
* 模块最终返回module.exports给调用者，而不是exports
* exports所做事情是收集属性,如果module.exports没有任何属性,exports会把其属性赋予module.exports
* module.exports存在一些属性,exports中所用的会被忽略

### 全局变量

* 全局对象
	* global 表示Node所在的全局环境，类似于浏览器中的window对象
	* process 指向Node内置的process模块，允许开发者与当前进程互动。例如你在DOS或终端窗口直接输入node，就会进入NODE的命令行方式（REPL环境）。退出要退出的话，可以输入 process.exit();
	* console 指向Node内置的console模块，提供命令行环境中的标准输入、标准输出功能。通常是写console.log()

* 全局函数
	*  定时器函数：共有6个，分别是setTimeout(), clearTimeout(), setInterval(), clearInterval()，setImmediate(),clearImmediate()。用于在指定毫秒之后，运行回调函数。实际的调用间隔，还取决于系统因素。间隔的毫秒数在1毫秒到2,147,483,647毫秒（约24.8天）之间。如果超过这个范围setImmediate()，会被自动改为1毫秒。该方法返回一个整数，代表这个新建定时器的编号
	*  require()：用于加载模块
	*  Buffer() 用于操作二进制数据

* 全局变量
	* _filename：指向当前运行的脚本文件名
	* _dirname：指向当前运行的脚本所在的目录

* 准全局变量 - 模块内部的局部变量，指向的对象根据模块不同而不同，但是所有模块都适用，可以看作是伪全局变量，主要为module, module.exports, exports等。module变量指代当前模块。module.exports变量表示当前模块对外输出的接口，其他文件加载该模块，实际上就是读取module.exports变量
	* module.id 模块的识别符，通常是模块的文件名
	* module.filename 模块的文件名
	* module.loaded 返回一个布尔值，表示模块是否已经完成加载
	* module.parent 返回使用该模块的模块
	* module.children 返回一个数组，表示该模块要用到的其他模块	  

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

**Aditional**

[Getting start with Nodejs](https://segmentfault.com/a/1190000002984199)
[i18n](https://segmentfault.com/a/1190000002632604)
[Session原理、安全以及最基本的Express和Redis实现](https://segmentfault.com/a/1190000002630691)
[sailjs](http://www.cnblogs.com/plzzhy/p/plzzhy.html)

###改善异步开发的方式###

###Generator###

* Generator函数是一个状态机，封装了多个内部状态。执行Generator函数返回一个遍历器对象，可依次遍历内部的每一个状态。
	* function关键字与函数名间有个星号
	* 函数体内使用yield语句，定义不同内部状态
	 
~~~javascript
function* helloWorldGenerator() {
	yield 'hello';
	yield 'world';
	return 'ending';
}
var hw = helloWorldGenerator();

hw.next(); // { value: 'hello', done: false }
hw.next(); // { value: 'world', done: false }
hw.next(); // { value: 'ending', done: true }
hw.next(); // { value: undefined, done: true }
~~~

Note: 

	* 调用Generator函数后，其并不执行，返回一个指向内部状态指针的对象(遍历器对象)
	* 需调用next方法，使指针移向下一个状态。(即到下一个yield或return语句为止)
	* next方法恢复执行，yield是暂停执行
	* next方法返回对象包含value和done2个值，value是当前内部状态的值，done表示十分遍历结束
	* yield语句后的表达式只有当调用next才会执行，等于为javascript提供手动的"惰性求值"的语法功能

~~~javascript
var arr = [1, [[2, 3], 4], [5, 6]];

var flat = function* (a) {
  var length = a.length;
  for (var i = 0; i < length; i++) {
    var item = a[i];
    if (typeof item !== 'number') {
      yield* flat(item);
    } else {
      yield item;
    }
  }
};

for (var f of flat(arr)) {
  console.log(f);
}
// 1, 2, 3, 4, 5, 6
~~~

* yield语句如果在一个表达式中，需放在圆括号中
~~~javascript
function* demo() {
  console.log('Hello' + yield); // SyntaxError
  console.log('Hello' + yield 123); // SyntaxError

  console.log('Hello' + (yield)); // OK
  console.log('Hello' + (yield 123)); // OK
}
~~~

* yield语句用作函数参数或放在赋值表达式右边，可不加括号

~~~javascript
function* demo() {
  foo(yield 'a', yield 'b'); // OK
  let input = yield; // OK
}
~~~

**Generator与Iterator接口**

* 因为Generator函数是遍历生成函数，因此可把Generator赋值给对象的Symbol.iterator属性，使其该对象具有Iterator接口。

~~~javascript
var myIterable = {};
myIterable[Symbol.iterator] = function* () {
  yield 1;
  yield 2;
  yield 3;
};

[...myIterable] // [1, 2, 3]
~~~

* Generator函数执行后返回一个遍历器对象，该对象也具有Symbol.iterator属性，执行后返回自身。

~~~javascript
function* gen(){
  // some code
}

var g = gen(); // 生成一个遍历器对象g

g[Symbol.iterator]() === g
// true
~~~

* 给next方法指定参数
	* yield语句本身无返回值，或总是返回undefined
	* 通过指定next方法的参数可以改变函数的行为

~~~javascript
function* f() {
	for (var i = 0; true; i++) {
		var reset = yield i;
		if (reset) i = -1;
	}
}
var g = f();
g.next() // { value: 0, done: false }
g.next() // { value: 1, done: false }
g.next(true) // { value: 0, done: false }
~~~

* 当指定为true后，i的值被重新设置为-1，下一轮循环从-1开始

~~~javascript
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}

var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}

var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }
~~~

Note:
	
* next方法参数表示上一个yield语句的返回值，所以第一个next不能带参数，V8引擎会直接忽略第一次使用next的参数
* 语义上讲，第一个next用来启动遍历器对象，所以不能带参数

**第一次调用next带参数**

~~~javascript
function wrapper(generatorFunction) {
  return function (...args) {
    let generatorObject = generatorFunction(...args);
    generatorObject.next();
    return generatorObject;
  };
}

const wrapped = wrapper(function* () {
  console.log(`First input: ${yield}`);
  return 'DONE';
});

wrapped().next('hello!')
// First input: hello!
~~~

**向Generator函数内部传值**

~~~javascript
function* dataConsumer() {
  console.log('Started');
  console.log(`1. ${yield}`);
  console.log(`2. ${yield}`);
  return 'result';
}

let genObj = dataConsumer();
genObj.next();
// Started
genObj.next('a')
// 1. a
genObj.next('b')
// 2. b
~~~

**for ... of**

~~~javascript
function* foo() {
	yield 1;
	yield 2;
	yield 3;
	yield 4;
	yield 5;
	return 6;
}
for (let v of foo()) {
	console.log(v);
}
// 1 2 3 4 5
~~~

Note: 

* for...of 根据返回对象的done属性判断是否结束循环，如果值为true，则循环中止，且不包含该返回对象。所以6不包含在内。
* 不使用next方法


**Generator.prototype.throw()**

* Generator函数返回遍历器对象，都有一个throw方法，可在函数体外抛出错误，然后在Generator函数体内捕获。

~~~javascript
var g = function* () {
  try {
    yield;
  } catch (e) {
    console.log('内部捕获', e);
  }
};

var i = g();
i.next();

try {
  i.throw('a');
  i.throw('b');
} catch (e) {
  console.log('外部捕获', e);
}
// 内部捕获 a
// 外部捕获 b
// 第一次被函数体内的catch捕获，第二次因函数体内的catch已执行无法捕获，此错误由外部的catch捕获
// throw方法可接受一个参数，该参数会被catch语句接收，建议抛出Error对象
// 如果Generator函数体内未使用try...catch捕获则会被外部try...catch捕获。
// 如果都未捕获，则程序直接报错，中断执行
// throw错误被捕获后，会附带执行下一条yield语句，即相当于执行一次next方法
var gen = function* gen(){
  try {
    yield console.log('a');
  } catch (e) {
    // ...
  }
  yield console.log('b');
  yield console.log('c');
}

var g = gen();
g.next() // a
g.throw() // b
g.next() // c
~~~

* 这种函数体内捕获错误的机制，大大方便了对错误的处理。多个yield语句，可以只用一个try...catch代码块来捕获错误。如果使用回调函数的写法，想要捕获多个错误，就不得不为每个函数内部写一个错误处理语句，现在只在Generator函数内部写一次catch语句就可以了。

**Generator.prototype.return()**

* 给返回给定值，且终结遍历Generator函数

~~~javascript
function* gen() {
	yield 1;
	yield 2;
	yield 3;
}

var g = gen();
console.log(g.next(),
g.return('foo'),
g.next());
~~~

####yield* 语句####

~~~javascript
function* foo() {
  yield 'a';
  yield 'b';
}

function* bar() {
  yield 'x';
  foo(); // 此处不能遍历到foo方法里的值
  yield 'y';
}

for (let v of bar()){
  console.log(v);
}
// "x"
// "y"
function* bar() {
  yield 'x';
  yield* foo();
  yield 'y';
}

// 等同于
function* bar() {
  yield 'x';
  yield 'a';
  yield 'b';
  yield 'y';
}

// 等同于
function* bar() {
  yield 'x';
  for (let v of foo()) {
    yield v;
  }
  yield 'y';
}

for (let v of bar()){
  console.log(v);
}
// "x"
// "a"
// "b"
// "y"
~~~

### 事件

* 所有能触发事件的对象都是EventEmitter类的实例
* 这些对象有on方法作为注册事件的方法，允许将1个或多个函数附加到会被对象触发的命名事件上
* 事件的名称通常是驼峰字符串，也可是任何有效的javascript属性名
* 当EventEmitter对象触发一个事件时，附加在特定事件上的函数会被同步的调用，被调用的监听器的返回值会被忽略或丢弃
* EventListener会按照监听器注册的顺序同步调用所有监听器，如果要使用异步，可使用setImmediate()或process.nextTick方法切换

###expressjs

- express.js

~~~javascript
/**
 * 用来生成app实例
 */
function createApplication() {
  var app = function(req, res, next) {
	// 将res, res逐级分发到express应用每个路由中，以便执行各个路由相匹配的操作
    app.handle(req, res, next);
  };
  // 将EventEmitter和application.js下的app类属性扩展到app上
  mixin(app, EventEmitter.prototype, false); 
  mixin(app, proto, false);
  // 将request,response和app对象添加到app属性上	
  app.request = { __proto__: req, app: app };
  app.response = { __proto__: res, app: app };
  app.init();  // 调用application.js 56行的init方法,用于初始化express应用设置
  return app;
}
~~~

- applicaton.js

编码规范

	* 回调惯例
		* 模块应优先公开一个错误优先(error-first)的回调接口 
		* 确保在回调中检查错误信息
		* 将回调函数返回
		* 仅在同步代码中使用try-catch

[Original](https://juejin.im/entry/5670bd9c60b294bccfdd4ec5)
