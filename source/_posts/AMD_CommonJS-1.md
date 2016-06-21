title: AMD 和 CommonJS -- 1. 基本概念
date: 2015-12-31 12:03:15
author: [mac.meng]
tags: [javascript,AMD,CommonJS]
---
### AMD = Asynchronous Module Definition

它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。

```
require([module], callback);

```
第一个参数[module]，是一个数组，里面的成员就是要加载的模块；第二个参数callback，则是加载成功之后的回调函数。如果将前面的代码改写成AMD形式，就是下面这样：

```
require([‘jquery’,’math'], function ($, math) {
	math.add(2, 3);
	$(body).show();
});
```

### CommonJS
那什么是CommonJS呢, 总是拿他和AMD来对比使用
2009年，美国程序员Ryan Dahl创造了node.js项目，将javascript语言用于服务器端编程。
node.js的模块系统，就是参照[CommonJS](http://wiki.commonjs.org/wiki/Modules/1.1)规范实现的。在CommonJS中，有一个全局性方法require()，用于加载模块。假定有一个数学模块math.js，就可以像下面这样加载。
```
var math = require('math');
math.add(2,3); // 5
```

### 那么问题来了
有了服务器端模块以后，很自然地，大家就想要客户端模块。而且最好两者能够兼容，一个模块不用修改，在服务器和浏览器都可以运行。
但是，由于一个重大的局限，使得CommonJS规范不适用于浏览器环境。
服务器端的文件依赖只取决于磁盘IO,但是客户端会有网络的不确定性.取决于网速的快慢，可能要等很长时间，浏览器处于"假死"状态。

因此，浏览器端的模块，不能采用"同步加载"（synchronous），只能采用"异步加载"（asynchronous）。这就是AMD规范诞生的背景.

### CommonJS 到底是个什么鬼

[CommonJS](http://www.commonjs.org/) 网站标题 : javascript: not just for browsers any more!

With CommonJS-compliant systems, you can use JavaScript to write:


* Server-side JavaScript applications
* Command line tools
* Desktop GUI-based applications
* Hybrid applications (Titanium, Adobe AIR)

感觉基本就是定义一些文档和API, 大家都遵循,自己去实现. 然后把JS写的到处都是,开枝散叶.

`CommonJS是服务器端模块的规范，Node.js采用了这个规范。`
根据CommonJS规范，一个单独的文件就是一个模块。 加载模块使用require方法，该方法读取一个文件并执行，最后返回文件内部的exports对象。

### 预告
接下来的几篇文章次会按照加载方式的不同,讲解一下 require.js 和 AngularJS 的加载机制 以及webwork 和 serverwork 的开发方式, 还有bower库和npm库的外壳的不同.
