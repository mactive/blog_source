title: JavaScript 朝花夕拾 -- 2 Require.JS 定义和发现机制
date: 2017-02-23 10:40:35
author: [mac.meng]
tags: [JavaScript,DawnBlossom,Series]
---

## Require.JS 定义和发现机制

很多jquery插件的头部都写成了这种样式,这种叫UMD,它支持AMD和CommonJS规范，同时还支持古老的全局模块模式。

[关于AMD,CMD,CommonJS及UMD规范](http://blog.gejiawen.com/2015/11/03/what-is-amd-cmd-commonjs-umd/)可以看这里.require.js和angular自带的使用的是标准的AMD方式.


```javascript
(function (factory) {
    if (typeof exports === "object" && exports &&
        typeof module === "object" && module && module.exports === exports) {
        // Browserify. Attach to jQuery module.
        factory(require("jquery"));
    } else if (typeof define === 'function' && define.amd) {
		// AMD. Register as an anonymous module.
		define(['jquery'], factory);
	} else {
		// Browser globals
		factory(jQuery);
	}
}(function ($) {
	//插件主体 一堆堆function
}))
```

moment.js 的头部则写成了这样,和上面都是UMD方案,都是为了让开发者在node端,浏览器端,浏览器+loader的情况下都可以使用到自己的模块.

```javascript
(function (global, factory) {
    typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
    typeof define === 'function' && define.amd ? define(factory) :
    global.moment = factory()
}(this, function () { 'use strict';
	// function 1
	// …
	// function n
	return moment;
}))
```

## 因为

1. 如果都是用define定义的模块,那么不用在shim
2. 如果不是用define写的模块,又有依赖,比如 jquery.datepair 依赖 jquery,需要写在shim中.

**shim的英文是垫片的意思,有类似转接环的概念.将非AMD方式定义的组件转义过来**

```javascript
require.config({
	shim: {
    'jquery.datepair':{
      'deps': [’jquery’,'datepair’] //需要写明依赖
    },
  }
});
```

### 加载非规范的模块
理论上，require.js加载的模块，必须是按照AMD规范、用define()函数定义的模块。但是实际上，虽然已经有一部分流行的函数库（比如jQuery）符合AMD规范，更多的库并不符合。
那么，require.js是否能够加载非规范的模块呢？
回答是可以的。

这样的模块在用require()加载之前，要先用require.config()方法的shim属性专门用来配置不兼容的模块。
举例来说，underscore和backbone这两个库，都没有采用AMD规范编写。如果要加载它们的话，必须先定义它们的特征。

deps[] 是他的的依赖,dependencies 的意思
shim的英文是垫片的意思,有类似转接环的概念.

```javascript
require.config({
	shim: {
		'underscore':{
			exports: '_'
		},
		'backbone': {
			deps: ['underscore', 'jquery'],
			exports: 'Backbone'
		},
		'jquery.scroll': {
		　　deps: ['jquery'],
		　　exports: 'jQuery.fn.scroll'
		}
	}
});
```

require.js 几个大概念

1. config() 配置依赖
2. define() 定义模块
3. require() 使用模块

define 一般自己用, 先声明配置,然后下面使用配置. 而且define一个模块的时候也可以引入依赖包,可以返回对象或者funtion,也可以给模块define一个名字
* Definition Functions with dependencies
* Definition a Module as a Functions or Object (Function 还是 Object 看业务需要,Object有的时候子对象也是function)
* Define a Module with a name (不推荐,因为module name和目录相关)

```javascript
define(["my/cart", "my/inventory"],
    function(cart, inventory) {
        //return a function to define "foo/title".
        //It gets or sets the window title.
        return function(title) {
            return title ? (window.title = title) :
                   inventory.storeName + ' ' + cart.name;
        }
    }
);
```

config一般和require一起用, config 定义好依赖之后, require可以直接使用它, require.js 会帮你加载你需要的依赖.

```javascript
requirejs.config({
    // 默认目录, require.js 会按相对目录去加载
    baseUrl: 'js/lib',
    paths: {
        app: '../app'
    }
});

// Start the main app logic.
requirejs(['jquery', 'canvas', 'app/sub'],
function   ($,        canvas,   sub) {
    //jQuery, canvas and the app/sub module are all
    //loaded and can be used here now.
});
```

