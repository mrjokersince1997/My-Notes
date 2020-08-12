# Node.js

https://www.runoob.com/nodejs/nodejs-tutorial.html

---

## Node.js 

### Node.js 介绍

Node.js 是一个 JavaScript 运行环境，运行在服务端（后端）。用户不再需要后台动态编程语言，只使用 JS 也可以创建自己的后台服务。

官网：https://nodejs.org/zh-cn/ 

> 默认下载可执行程序版本，程序会自动配置环境变量，开发者可以直接使用。

### Node.js 使用

安装 Node.js 成功后，通过控制台命令可以执行 JS 脚本：

```bash
node --version             # 查看 Node.js 版本

node                       # 进入 Node.js 运行环境，可以直接输入 JS 代码并执行
node example.js            # 执行指定的 JS 文件
```

---

## Node.js 模块

### 模块化开发

根据主流的模块化规范 ES6 定义：一个 JS 文件就是一个模块。

模块之间必须通过规范的接口相互调用，目前最常使用的是 CommonJS 规范和 ES6 标准。

**CommonJS 规范**

1. module.exports 导出接口，require 引入模块。
2. 输出是值的拷贝。

*由于引擎尚不支持，我们在 node.js 中习惯使用 CommonJS 语法。*

```js
  var num = 1
  function test(x){ 
    console.log("输出数据为" + x); 
  }

  exports.num = num
  exports.test = userTest           // 可以写为 module.exports.test = useTest

  /*************************************************************************/

  var a = require('./a')            // 调用 a.useTest(a.num)   
```

**ES6 语法**

1. export 导出接口，import 引入模块。
2. 输出是值的引用。

*我们在 vue 中通常使用 ES6 语法，但必须安装 babel 插件自动转码为 commonJS 语法执行。*

```js
  var num = 1
  function test(x){ 
    console.log("输出数据为" + x); 
  }

  export var num                  
  export function test               // 可以写为 export test

  // 默认导出接口
  export default {            
    num,
    test
  }     

  /*************************************************************************/

  import {num,test as useTest} from './a.js'   // 调用 useTest(num)

  // 读取 JS 文件全部导出
  import * as a from './a.js'                  // 调用 a.test(a.num)

  // 读取 JS 文件默认导出
  import a from './a.js'           
```


### 官方模块

Node.js 提供了允许直接导入的官方模块，查询网址：http://nodejs.cn/api/


- **OS 模块**：系统信息

```js
var os = require("os");

console.log('主机名' + os.hostname());
console.log('操作系统' + os.type());
console.log('系统平台' + os.platform());
console.log('内存总量' + os.totalmem() + '字节');
console.log('可用内存' + os.freemem() + '字节');
```

- **Path 模块**：路径信息

```js
var path = require("path");

var data = "c:/myapp/index.html";
console.log(path.basename(data));          // 输出 index.html
console.log(path.dirname(data));           // 输出 c:/myapp
console.log(path.basename(dirname(data))); // 输出 myapp
```

- **URL 模块**：URL 信息

```js
var url = require("url");

var data = "http://test.com?name=王东浩&age=22";
console.log(url.parse(data));             // 输出 网址解析信息
var urlQuery = url.parse(data, true);
console.log(urlQuery.query.name);         // 输出 王东浩
```

- **fs 模块**：文件信息

```js
var fs = require("fs");

fs.writeFile('./a.txt', '你好，我是王东浩', function(err){
  if(err){
    console.log(err);
    return;
  }
  console.log('success'); 
})

fs.readFile('./a.txt', 'utf8', function(err, data){
  if(err){
    console.log(err);
    return;
  }
  console.log(data); 
})
```

- **http / https 模块**：服务器

```js
var http = require("http");

var server = http.createserver();             // 创建服务器

server.on('request', function(req, res){      // 配置监听器，有请求则触发
  console.log('收到用户请求，请求地址' + req.url)
  console.log('请求方式' + req.method)
  
  if(req.url == '/'){ 
    $msg = "<a herf='www.baidu.com'>点击跳转</a>";
  }else{
    $msg = "404 Not Found";
  }
  res.setHeader('Content-Type','text/html,charset=utf8');
  res.write($msg);
  res.end;
})

server.listen(8080, function(){               // 启动服务器，监听端口
  console.log('服务启动成功')
})
```

---

## Node.js 项目

### 包管理工具 npm

用于 Node.js 项目的开发和管理，包括安装和卸载第三方模块。是非常强大和常用的 Node.js 工具。下载安装 Node.js 后，即可在控制台使用 npm 命令。

- **初始化项目**

```bash
npm init -y                       # 将当前目录初始化为项目，即生成 package.json 配置文件。
```

> package.json 配置文件负责管理 Node.js 项目。

- **模块安装**

```bash
npm install vue/cli               # 安装指定的 Node.js 模块
npm uninstall vue/cli             # 卸载指定的 Node.js 模块

npm install                       # 安装该项目 package.json 记录的所有模块
```

> node_modules 文件夹负责存储当前 Node.js 项目要使用的模块。


npm 下载模块时默认安装在本地，只允许当前目录的项目调用。我们可以对模块进行全局安装，允许所有项目调用。

```bash
npm install vue/cli -g            # 全局安装 Node.js 模块
npm uninstall vue/cli -g          # 全局卸载 Node.js 模块
```

修改和查看 npm 下载地址。

```bash
npm --registry https://registry.npm.taobao.org install express   # 临时使用

npm config set registry https://registry.npm.taobao.org
npm config get registry 
```

- **开发环境**

npm 下载模块时默认为通用模块，可以在全部开发环境下使用。但我们在开发时可能需要引入部分模块，完成后不在生产环境引用。

在项目的 package.json 配置文件中，调用的通用模块会记录在 dependencies 参数中，而开发用模块会记录在 devdependencies 参数中。

```bash
npm install vue/cli --save-dev           # 安装指定的 Node.js 模块，并标记为开发专用

npm install --production                 # 安装该项目 package.json 记录的通用模块，但不安装开发用模块
```

### 配置文件 package

package.json 配置文件负责记录 Node.js 项目信息。

```json
{
  // 项目名称
  "name": "example", 
  // 项目版本
  "version": "1.0.0",
  // 项目描述 
  "description": "我的 Node 项目",
  // 入口文件
  "main": "app.js",
  // 引入模块
  "dependencies": {
    "koa": "^2.0.0",
    "koa-router": "^7.4.0",
    "mysql": "^2.17.1"
  },
  "devDependencies": {},
  // 快捷命令
  "scripts": {
    "error": "echo \"Error: no test specified\" && exit 1",
    "start": "node app.js"          
  },
  // 作者和凭证
  "author": "MrJoker",
  "license": "ISC"
}
```

1. **入口文件**

尝试启动项目时，系统会自动调用根目录下的入口文件执行。默认为 app.js 文件，在项目目录下控制台输入 `node app.js` 即可运行服务器。

2. **快捷命令**
 
将 JS 脚本封装为 npm 命令，在项目目录下控制台输入 `npm run start` 即可运行服务器。



### 打包工具 webpack

webpack 是基于 Node.js 的自动化构建工具，对整个项目要请求的静态资源进行合并、封装、压缩、和自动转换。大大加快了请求静态资源页面加载速度，也解决了代码的兼容性问题。
