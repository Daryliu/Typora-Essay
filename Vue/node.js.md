### node.js（面向服务端）

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

fs.readFile('xxx',function(error,data){//读取xxx文件,第一个参数是要读取的文件路径，第个参数是回调函数。（error若读取成功，error为null，若读取失败error就是错误对象     data若读取成功，data为读取到的对象，若读取失败data就是undefined）
    if(error) {//此处用到的是非阻塞代码示例
        console.log('读取文件失败');
    } else {
        console.log(data.toString());
    }
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

`module.exports`用来**默认导出**一个对象，没有指定对象名，常见于修改模块的原始导出对象，比如原本模块导出的一个对象，可以通过module.exports修改为导出一个函数。<u>公开了它指向的对象</u>

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
let readline = require('readline');//readline模块每次一行的哦那个可读流获取输入
```

`question()`方法显示第一参数并等待用户的输入

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

