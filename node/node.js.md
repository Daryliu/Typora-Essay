### node.js（面向服务端）

node.js是单进程单线程应用程序，通过事件和回调可支持并发。每一个 API 都是异步的，并作为一个独立线程运行，使用异步函数调用，并处理并发。

没有dom和bom

#### 1、通过命令行运行Node.js脚本（找到文件所在位置）

```js
node add.js（文件名）
```

当运行命令时，请确保位于包含 `app.js` 文件的目录中。

#### 2、Node.js的组成：

​		1> 引入required模块

​		2> 创建服务器

​		3> 接受请求和响应请求

#### 3、读取文件

​		浏览器中的js是没有文件操作的能力的，**node中的js可操作文件**

```js
//fs是file-system，在node中若想对文件操作，必须引入fs，其提供了文件操作相关的API
var fs = require('fs');//require方法 加载文件操作的fs核心模块
//异步读文件
fs.readFile('xxx','utf-8',function(error,data){//读取xxx文件,第一个参数是要读取的文件路径，第个参数是回调函数。（error若读取成功，error为null，若读取失败error就是错误对象     data若读取成功，data为读取到的对象，若读取失败data就是undefined）
    if(error) {//此处用到的是非阻塞代码示例
        console.log('读取文件失败');
    } else {
        console.log(data.toString());
    }//若文件是二进制文件时，回调函数的data参数返回一个Buffer对象，即是包含0个或任意个字节的数组。
});
```

##### 读取目录

```js
var fs = require("fs");
//异步读取
fs.readdir("./tmp",function(err,files){
    if(err) {
        return console.error(err);
    }
    files.foreach(function (file){
        console.log(file);//循环读取files中的文件
    })
})
```

##### 删除目录

```js
var fs = require("fs");

fs.rmdir("../../tmp",function(err){
    if(err) {
        console.error(err);
    }
    console.log("删除")；
})
```

##### 删除文件

```js
var fs = require("fs");

console.log("准备删除文件！");
fs.unlink('input.txt', function(err) {//unlink用于删除
   if (err) {
       return console.error(err);
   }
   console.log("文件删除成功！");
});
```



#### 4、写文件

```js
var fs = require('fs');
//第一个参数是文件路径    第二个参数是写入的文件内容   第三个参数是回调函数
fs.writeFile('./data/hello.txt','大家好，给大家拜年了',function(error) {
    //接收参数error  （写入成功error为null，失败为错误对象）
    if(error) {
        console.log('写入文件失败');
    }else {
        console.log('文件写入成功');
    }
});
```

#### 5、导入包

【2引入文件】`require`函数用来在一个模块中引入另一个模块，传入一个模块名，返回一个模块导出对象，用法：`let cc = require('模块名');`

【1公开文件】`exports`对象用来**指定<font color="greenyellow">单个</font>>导出**当前模块的公共方法或属性，用法：`exports.name`，name为导出的对象名（系统默认设置了`exports=module.exports`）<u>公开了它指向的对象的属性</u>

```js
exports.hello = hello;
exports.greet = greet;
```

`module.exports`用来**默认导出**一个函数、数组或对象，没有指定对象名，常见于修改模块的原始导出对象，比如原本模块导出的一个对象，可以通过module.exports修改为导出一个函数。<u>公开了它指向的对象</u>

```js
module.exports = {
    hello: hello,
    greet: greet
};
```

#### 6、Node.js REPL交互式解释器

​		类似Windows系统的终端和Linux的Shell，可以直接在控制台输入代码执行	

```js
//例子：在cmd中输入node
//再执行 var x = 0;等
console.count()元素计数
console.clear() 会清除控制台
console.trace()打印堆栈踪迹
time()计算耗时
timeEnd()计算耗时
REPL命令：
ctrl + c - 退出当前终端。
ctrl + c 按下两次 - 退出 Node REPL。
ctrl + d - 退出 Node REPL.
向上/向下 键 - 查看输入的历史命令
tab 键 - 列出当前命令
.help - 列出使用命令
.break - 退出多行表达式
.clear - 退出多行表达式
.save filename - 保存当前的 Node REPL 会话到指定文件
.load filename - 载入当前 Node REPL 会话的文件内容。
```

#### 7、在node中从命令行接收输入

```js
let readline = require('readline');//readline模块每次一行的从可读流获取输入（也就是从终端中获取输入输出）
//实例化接口对象
var rl = readline.createInterface({
    input: process.stdin,  //设置输入输出为：进程终端的输入输出
    output: process.stdout
});
//question方法
rl.question("你的名字是",function(answer){//`question()`方法显示第一参数并等待用户的输入
    console.log("答案是："+answer);
    rl.close();
})
//close事件监听
rl.on("close",function(){
    //表示close时结束程序
    process.exit(0);
})
```

#### 8、npm包管理器

`npm` 可以管理项目依赖的下载。

- `--save` 安装并添加条目到 `package.json` 文件的 dependencies。
- `--save-dev` 安装并添加条目到 `package.json` 文件的 devDependencies。

运行任务：

npm run 任务名(如dev/start)

其中package.json文件中会设置指定命令行任务的格式：

```json
{
  "scripts": {
    "watch": "webpack --watch --progress --colors --config webpack.conf.js",
    "dev": "webpack --progress --colors --config webpack.conf.js",
    "prod": "NODE_ENV=production webpack -p --config webpack.conf.js",
  },
}

//在命令行即可执行npm run dev或npm run watch或npm run prod;
```

##### npm将软件安装的位置问题

- 本地安装
- 全局安装

```js
//1  默认情况下  输入npm install lodash时
软件包会被安装到当前文件树中的 node_modules 子文件夹下。
在这种情况下，npm 还会在当前文件夹中存在的 package.json 文件的 dependencies 属性中添加 lodash 条目。

//2  全局安装时npm install -g lodash
Windows上安装的位置是C:\Users\dn3\AppData\Roaming\npm\node_modules
```

#### 9、基本模块

1、`process`也是Node.js提供的一个对象，它**代表当前Node.js进程**。通过`process`对象可以拿到许多有用信息：

```js
> process === global.process;
true
> process.version;
'v5.2.0'
> process.platform;
'darwin'
> process.arch;
'x64'
> process.cwd(); //返回当前工作目录
'/Users/michael'
> process.chdir('/private/tmp'); // 切换当前工作目录
undefined
> process.cwd();
'/private/tmp'
```

在下一次**事件循环**响应中执行代码，可以调用`process.nextTick()`

```js
// process.nextTick()将在下一轮事件循环中调用:
process.nextTick(function () {
    console.log('nextTick callback!');
});
console.log('nextTick was set!');
打印输出：
nextTick was set!
nextTick callback!
```

##### 9.1 fs

如果我们要获取文件大小，创建时间等信息，可以使用`fs.stat()`，它返回一个`Stat`对象，能告诉我们文件或目录的详细信息

```js
var fs = require('fs');

fs.stat('sample.txt', function (err, stat) {
    if (err) {
        console.log(err);
    } else {
        // 是否是文件:
        console.log('isFile: ' + stat.isFile());
        // 是否是目录:
        console.log('isDirectory: ' + stat.isDirectory());
        if (stat.isFile()) {
            // 文件大小:
            console.log('size: ' + stat.size);
            // 创建时间, Date对象:
            console.log('birth time: ' + stat.birthtime);
            // 修改时间, Date对象:
            console.log('modified time: ' + stat.mtime);
        }
    }
});
```

##### 9.2  stream（流）

含义：相当于读取大数据时像河流一样一点一点读取，防止吃内存

**流类型：**

- **Readable** - 可读操作。
- **Writable** - 可写操作。
- **Duplex** - 可读可写操作.
- **Transform** - 操作被写入数据，然后读出结果。

所有的 Stream 对象都是 EventEmitter 的实例。常用的事件有：

- **data** - 当有数据可读时触发。
- **end** - 没有更多的数据可读时触发。
- **error** - 在接收和写入过程中发生错误时触发。
- **finish** - 所有数据已被写入到底层系统时触发。

##### 从流中读取数据

```js
var fs = require("fs");
var data = '';

// 创建可读流
var readerStream = fs.createReadStream('input.txt');

// 设置编码为 utf8。
readerStream.setEncoding('UTF8');

// 处理流事件 --> 流有data, end,  error三种状态
readerStream.on('data', function(chunk) {     //chunk是一段一段流入的文件流
   data += chunk;
});

readerStream.on('end',function(){
   console.log(data);
});

readerStream.on('error', function(err){
   console.log(err.stack);
});

console.log("程序执行完毕");
```

##### 写入流

```js
var fs = require("fs");
var data = 'www.sxt.com';

// 创建一个可以写入的流，写入到文件 output.txt 中
var ws = fs.createWriteStream('output.txt',{flags:"w",encoding:"utf-8"});       //文件路径		flags:"w"表示写

// 使用 utf8 编码写入数据
ws.write("写入hellonode.js",'UTF8');      //文件配置

// 标记文件末尾
ws.end();

ws.on('open', function() {//监听打开事件
    console.log("文件打开了。");
});

ws.on('close', function() {//监听关闭事件
    console.log("文件关闭了。");
});

// 处理流事件 --> data, end, error
ws.on('finish', function() {
    console.log("写入完成。");
});

ws.on('error', function(err){
   console.log(err.stack);
});

console.log("程序执行完毕");
```

##### 管道流

管道提供了一个输出流到输入流的机制。<u>通常我们用于从一个流中获取数据并将数据传递到另外一个流中。</u>

```js
var fs = require("fs");

// 创建一个可读流
var rs = fs.createReadStream('input.txt');

// 创建一个可写流
var ws = fs.createWriteStream('output.txt');

// 管道读写操作
// 读取 input.txt 文件内容，并将内容写入到 output.txt 文件中
rs.pipe(ws);

console.log("程序执行完毕");
```

##### 链式流

<u>链式是通过连接输出流到另外一个流并创建多个流操作链的机制</u>。链式流一般用于管道操作。

```js
//使用管道和链式压缩文件
var fs = require("fs");
var zlib = require('zlib');

// 压缩 input.txt 文件为 input.txt.gz
fs.createReadStream('input.txt')
  .pipe(zlib.createGzip())
  .pipe(fs.createWriteStream('input.txt.gz'));
  
console.log("文件压缩完成。");
```

```js
//使用管道和链式解压文件
var fs = require("fs");
var zlib = require('zlib');

// 解压 input.txt.gz 文件为 input.txt
fs.createReadStream('input.txt.gz')
  .pipe(zlib.createGunzip())
  .pipe(fs.createWriteStream('input.txt'));
  
console.log("文件解压完成。");
```

#### 10、node事件

node是单线程单进程应用程序；有回调的都是事件。其都是用<u>观察者模式</u>实现；

##### 事件驱动程序

若Node.js 有多个内置的事件，我们可以通过引入 events 模块，并通过实例化 EventEmitter 类来绑定和监听事件，如下实例：

```js
// 引入 events 模块  处理多个内置的事件
var events = require('events');
// 创建 eventEmitter 对象
var eventEmitter = new events.EventEmitter();
// 绑定事件及事件的处理程序
eventEmitter.on('eventName', eventHandler);
// 触发事件
eventEmitter.emit('eventName');
```







#### 11、路径模块和系统模块

`path`提供对<u>路径</u>的操作，`os`提供了<u>操作系统</u>相关的API。

##### path

```js
let path = require('path')
path.extname()获取文件/路径文件的扩展名  //如获取.md  .txt											1

path.resolve([...paths])   //把一个路径或路径片段的序列解析从左往右处理为一个绝对路径					2  ...是解构数组
例子：path.resolve('foo','/baz','bar');  //完成后为'C:\baz\bar'   
	
path.join([...paths])  //使用平台特定的分隔符把全部给定的path片段连接到一起并生成规范化的路径（常用）		3
参数1：__dirname获取当前执行文件所在目录完整目录名
参数：__filename获得当前执行文件的带有完整绝对路径的文件名（到本文件名）
process.cwd()获得当前执行的node命令时候的文件夹目录名
例子1：path.join(__dirname,'./02art-template.js')
//结果为'C:\Users\dn3\Desktop\nodestudy\02art-template.js'
例子2：path.join('/foo','bar','./baz')
//结果为 '/foo/bar/baz'
```

##### os

```js
let os = require('os')
os.cpus()//获得操作系统的cpu信息
```



#### 12、创建编写服务器

##### http

```js
var http = require('http')			//		1 创建server
var server = http.createServer()//创建一个web服务器，返回一个server实例

//2监听request请求事件，设置处理函数
//当客户端请求过来，自动触发服务器request请求事件，然后执行并回调function函数		  				//第3步服务器启动后输入网址就会触发
server.on('request',function(request,response){	// 函数中request获取客户端请求信息，如请求路径request.url等；response发送响应信息
    conlose.log('收到客户端的请求，路径是' + request.url)   	
    
    //response有一个方法write可以给客户端发送响应数据，write可以多次，但是要有end结束响应，否则会一致等待
    //response.write('hello node.js')
    //response.end()
    
    //-----------------结合不同的url来实现不同的响应内容----------------------------
    var URL = req.url;  //定义URL为请求路径
    if(URL === "/"){
        // res.write('hello！欢迎来到首页')
        // res.end()

        // 上面的方式比较麻烦，推荐使用更简单的方式，直接 end 的同时发送响应数据
        // res.end('hello！欢迎来到首页') 在响应结束同时发送数据
        res.end("hello！欢迎来到首页")
    } else if(URL === "/page1"){
        res.end("这里是召唤师峡谷")
    } else if(URL === "/page2"){
        res.end("这里是王者峡谷")
    } else{
        res.end("404 ！ 恭喜恭喜，峡谷不见了") //404 没有url地址时返回
    } 
}) 

//3	绑定端口号，启动服务器						
server.listen(3000,function(){
    conlose.log('服务器启动成功！')
})

```









