---
layout: default
title: "Unclassified | CommonJS规范"
description: "CommonJS规范是Node.js处理模块的标准"
---

> [内容大部分都是来源于 @阮一峰老师的博客 CommonJS规范，这里只是当一个笔记的作用](https://javascript.ruanyifeng.com/nodejs/module.html)

# 概述

Node应用由模块构成，采用CommonJS模块规范

每个文件就是一个模块，有自己的作用域。在一个文件里面定义的变量、函数和类都是私有的，对其他文件不可见

```js
var x = 5;
var addX = function (value) {
  return value + x;
};
```

如果想让`x`变量和`addX`函数对其他文件公开，可以将其设定为`global`对象的属性

```js
global.y = 10;
```

上面代码的y变量，可以被所有文件读取，但这样的写法是不推荐的

CommonJS规范规定，每个模块内部，`module`变量代表当前模块。这个变量是一个对象，它的`exports`属性是对外的接口。加载某个模块，其实是加载该模块的`module.exports`属性

```js
var x = 5;
var addX = function (value) {
  return value + x;
};
module.exports.x = x;
module.exports.addX = addX;
```

上述代码将`x`变量和`addX`函数公开了出来

可以使用`require`方法加载该模块

```js
var example = require('./example.js');

console.log(example.x); // 5
console.log(example.addX(1)); // 6
```

CommonJS模块的特点:

> 所有代码都在模块内运行，不会污染全局作用域

> 模块多次加载，但是运行结果在第一次加载的时候就被缓存了，后续的加载都是直接从缓存里读取结果。除非清楚缓存结果，模块才会再次运行

> 模块加载的顺序，按照其在代码中出现的顺序

## module对象

Node内部提供一个`Modele`构造函数。所有模块都是它的实例。

```js
function Module(id, parent) {
  this.id = id;
  this.exports = {};
  this.parent = parent;
  // ...
```
> `module.id`模块的识别符，通常是带有绝对路径的模块文件名。  
`module.filename`模块的文件名，带有绝对路径。  
`module.loaded`返回一个布尔值，表示模块是否已经完成加载。  
`module.parent`返回一个对象，表示调用该模块的模块。  
`module.children`返回一个数组，表示该模块要用到的其他模块。  
`module.exports`表示模块对外输出的值。

下面是一个示例文件，最后一行输出module变量

```js
var jquery = require('jquery');
exports.$ = jquery;
console.log(module);
```

输出结果为

```js
{ 
  id: '.',
  exports: { '$': [Function] },
  parent: null,
  filename: '/path/to/example.js',
  loaded: false,
  children:
   [ { id: '/path/to/node_modules/jquery/dist/jquery.js',
       exports: [Function],
       parent: [Circular],
       filename: '/path/to/node_modules/jquery/dist/jquery.js',
       loaded: true,
       children: [],
       paths: [Object] } ],
  paths:
   [ '/home/user/deleted/node_modules',
     '/home/user/node_modules',
     '/home/node_modules',
     '/node_modules' ]
}
```

## module.exports属性

`module.exports`属性表示当前模块对外输出的接口，其他文件加载该模块，实际上就是读取`module.exports`变量。

## exports变量

为了方便，Node为每个模块提供一个exports变量，指向module.exports。这等同在每个模块头部，有一行这样的命令。

```js
var exports = module.exports;
```

`exports`是一个变量，其也有被赋值的一天

```js
exports.hello = function() {
  return 'hello';
};

module.exports = 'Hello world';
```

这样但调用`module.exports.hello`的时候是无法输出的，因为`module.exports`被重新赋值了

*****

> 暂未理解START 

# AMD规范与CommonJS规范的兼容性

CommonJS规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作。AMD规范则是非同步加载模块，允许指定回调函数。由于Node.js主要用于服务器编程，模块文件一般都已经存在于本地硬盘，所以加载起来比较快，不用考虑非同步加载的方式，所以CommonJS规范比较适用。但是，如果是浏览器环境，要从服务器端加载模块，这时就必须采用非同步模式，因此浏览器端一般采用AMD规范。

AMD规范使用define方法定义模块，下面就是一个例子：
```js
define(['package/lib'], function(lib){
  function foo(){
    lib.log('hello world!');
  }

  return {
    foo: foo
  };
});
```
AMD规范允许输出的模块兼容CommonJS规范，这时define方法需要写成下面这样：
```js
define(function (require, exports, module){
  var someModule = require("someModule");
  var anotherModule = require("anotherModule");

  someModule.doTehAwesome();
  anotherModule.doMoarAwesome();

  exports.asplode = function (){
    someModule.doTehAwesome();
    anotherModule.doMoarAwesome();
  };
});
```

> 暂未理解END

*****

# require命令

## 基本用法

Node使用CommonJS模块规范，内置的`require`命令用于加载模块文件。

`require`命令的基本功能是，读入并执行一个JavaScript文件，然后返回该模块的exports对象。如果没有发现指定模块，会报错。

```js
// example.js
var invisible = function () {
  console.log("invisible");
}

exports.message = "hi";

exports.say = function () {
  console.log(message);
}
```

## 加载规则

```js
var foo = require('foo');
//  等同于
var foo = require('foo.js');
```

> 如果参数字符串以"/"开头，则表示加载的是一个位于绝对路径的模块文件。比如，require('/home/marco/foo.js')将加载/home/marco/foo.js。

> 如果参数字符串以"./"开头，则表示加载的是一个位于相对路径（跟当前执行脚本的位置相比）的模块文件。比如，require('./circle')将加载当前脚本同一目录的circle.js。

> 如果参数字符串不以"./"或"/"开头，则表示加载的是一个默认提供的核心模块（位于Node的系统安装目录中），或者一个位于各级node_modules目录的已安装模块（全局安装或局部安装）。

举例来说，脚本/home/user/projects/foo.js执行了require('bar.js')命令，Node会依次搜索以下文件。
```js
/usr/local/lib/node/bar.js
/home/user/projects/node_modules/bar.js 
/home/user/node_modules/bar.js
/home/node_modules/bar.js
/node_modules/bar.js
```
这样设计的目的是，使得不同的模块可以将所依赖的模块本地化。

> 如果参数字符串不以"./"或"/"开头，而且是一个路径，比如require('example-module/path/to/file')，则将先找到example-module的位置，然后再以它为参数，找到后续路径。

> 如果指定的模块文件没有发现，Node会尝试为文件名添加.js、.json、.node后，再去搜索。.js文件会以文本格式的JavaScript脚本文件解析，.json文件会以JSON格式的文本文件解析，.node文件会以编译后的二进制文件解析。

> 如果想得到require命令加载的确切文件名，使用`require.resolve()`方法。

## 模块的缓存

所有缓存的模块保存在require.cache之中

```js
// 删除指定模块的缓存
delete require.cache[moduleName];

// 删除所有模块的缓存
Object.keys(require.cache).forEach(function(key) {
  delete require.cache[key];
})
```

## 模块的循环加载

如果发生模块的循环加载，即A加载B，B又加载A，则B将加载A的不完整版本。

```js
// a.js
exports.x = 'a1';
console.log('a.js ', require('./b.js').x);
exports.x = 'a2';

// b.js
exports.x = 'b1';
console.log('b.js ', require('./a.js').x);
exports.x = 'b2';

// main.js
console.log('main.js ', require('./a.js').x);
console.log('main.js ', require('./b.js').x);
```

## require.main

`require`方法有一个`main`属性，可以用来判断模块是直接执行还是被调用执行

```js
require.main === module
// true
```

# 模块的加载机制

CommonJS模块的加载机制是，输入的是被输出的值的拷贝。也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。

```js
// lib.js
var counter = 3;
function incCounter() {
  counter++;
}
module.exports = {
  counter: counter,
  incCounter: incCounter,
};
```

这里的会有一个+1的函数，讲道理调用它的时候，counter会+1

```js
// main.js
var counter = require('./lib').counter;
var incCounter = require('./lib').incCounter;

console.log(counter);  // 3
incCounter();
console.log(counter); // 3
```

`counter`输出以后，`lib.js`模块内部的变化就影响不到`counter`了。

## require的内部处理流程

`require`命令是`CommonJS`规范之中用来加载其他模块的命令。是指向当前模块的`module.require`命令，而`module.require`又调用Node的内部命令`Module._load`

```js
Module._load = function(request, parent, isMain) {
  // 1. 检查 Module._cache，是否缓存之中有指定模块
  // 2. 如果缓存之中没有，就创建一个新的Module实例
  // 3. 将它保存到缓存
  // 4. 使用 module.load() 加载指定的模块文件，
  //    读取文件内容之后，使用 module.compile() 执行文件代码
  // 5. 如果加载/解析过程报错，就从缓存删除该模块
  // 6. 返回该模块的 module.exports
};
```

上面的第4步，采用module.compile()执行指定模块的脚本，逻辑如下。

```js
Module.prototype._compile = function(content, filename) {
  // 1. 生成一个require函数，指向module.require
  // 2. 加载其他辅助方法到require
  // 3. 将文件内容放到一个函数之中，该函数可调用 require
  // 4. 执行该函数
};
```


> `require()`: 加载外部模块  
`require.resolve()`：将模块名解析到一个绝对路径  
`require.main`：指向主模块  
`require.cache`：指向所有缓存的模块  
`require.extensions`：根据文件的后缀名，调用不同的执行函数  

一旦`require`函数准备完毕，整个所要加载的脚本内容，就被放到一个新的函数之中，这样可以避免污染全局环境。该函数的参数包括`require`、`module`、`exports`，以及其他一些参数。

```js
(function (exports, require, module, __filename, __dirname) {
  // YOUR CODE INJECTED HERE!
});
```

`Module._compile`方法是同步执行的，所以`Module._load`要等它执行完成，才会向用户返回`module.exports`的值。