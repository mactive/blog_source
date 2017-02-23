title: JavaScript 朝花夕拾 -- 1
date: 2016-08-08 12:40:35
author: [mac.meng]
tags: [JavaScript,DawnBlossom,Series]
---
## Apply Call Argument
call 和 apply 都是为了改变某个函数运行时的 context 即上下文而存在的，换句话说，就是为了改变函数体内部 this 的指向。因为 JavaScript 的函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。

二者的作用完全一样，只是接受参数的方式不太一样。例如，有一个函数 func1 定义如下：

```JavaScript
var func1 = function(arg1, arg2) {};
```

就可以通过 func1.call(this, arg1, arg2); 或者 func1.apply(this, [arg1, arg2]); 来调用。其中 this 是你想指定的上下文，他可以任何一个 JavaScript 对象(JavaScript 中一切皆对象)，call 需要把参数按顺序传递进去，而 apply 则是把参数放在数组里。

JavaScript 中，某个函数的参数数量是不固定的，因此要说适用条件的话，当你的参数是明确知道数量时，用 call，而不确定的时候，用 apply，然后把参数 push 进数组传递进去。当参数数量不确定时，函数内部也可以通过 arguments 这个数组来便利所有的参数。

```JavaScript
function splat(fun){
	return function(array){
		return fun.apply(null,array); // null 没有上下文,等待参数传入
		// apply 只能接收数组类型的参数
	}
}

var addArrayElements = splat(function(x, y){ return x + y});
addArrayElements([1,2]);
// 3
```
## bind - Function.prototype.bind()
bind() 方法与 apply 和 call 很相似 是可以改变函数体内 this 的指向。
react es6 撰写方式的代码经常需要 `.bind(this)`, 绑定作用域

MDN的解释是：bind()方法会创建一个新函数，称为绑定函数，当调用这个绑定函数时，绑定函数会以创建它时传入 bind()方法的第一个参数作为 this，传入 bind() 方法的第二个以及以后的参数加上绑定函数运行时本身的参数按照顺序作为原函数的参数来调用原函数。

```JavaScript
var foo = {x:3};
var bar = function(){
    console.log(this.x);
}
 
bar(); // undefined
var func = bar.bind(foo);
func(); // 3
```
这里我们创建了一个新的函数func, 当使用bind绑定一个作用域之后.当func被执行的时候实际上是bar被执行.
然后bar中的this 指向的是foo. 

如果你不想新建func也行,写作 bar.bind(foo)(). 因为bind是function原型中的方法.所以必须是一个
function类型的对象.bind(xx).当然他也返回一个function类型的对象

## Exception throw try catch
### Rules
1. 所有try中的执行代码,包括func中的,只要有抛出 Error,就会被catch住,一旦有throw,throw之后的代码就不会执行
2. 如果try catch 有嵌套关系,那么只有同级的catch会处理try中throw出来的异常,如果想向上传递需要在catch中 继续throw,确保他的外侧能接受到
3. try catch 经常和 finally[一起使用](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch) 
4. 系统内置了一写 Error,例如TypeError,ReferenceError,EvalError,SyntaxError
 

```
var doSome = function(thing){
   throw new Error("UserException: "+thing);
}
try{
  console.log('here i try');
  doSome("eat"); // 执行到这个throw的时候会停止执行
  throw new TypeError("this is a TypeError");
}
catch(e){
  if(e instanceof TypeError){
     console.log(e.message,e.name);
  }else {
     console.log(e.message,e.name);
  }
}
```








