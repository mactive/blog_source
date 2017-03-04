title: JavaScript 朝花夕拾 -- 3 浏览器中JavaScript的好基友
date: 2017-03-04 10:15:05
author: [mac.meng]
tags: [JavaScript,DawnBlossom,Series]
---

最近面试发现很多新人对DOM Event甚至DOM 都不怎么了解.在此科普一下
浏览器厂商和W3C标准为广大的JS开发者准备了写什么. 浏览器开发是业界公认的开发难度仅次于操作系统的项目,google和mozilla都排出了顶级的工程师参与开发,并诞生了 webkit和v8 engine等伟大的开源项目应用在PC和移动端,微软也从标准的阻碍者逐渐变成了标准追随者(⊙﹏⊙)b


## DOM Document Object Model

> DOM 并不只是针对 JavaScript 的,很多别的语言也都实现了 DOM.

* DOM0: 历史基准点并不存在
* DOM1: 映射文档的结构, DOM Core + DOM HTML
* DOM2: 在DOM1的基础上增加了
* * DOM Event: 定义了事件和事件处理的接口
* * DOM Style: 定义了基于 CSS 为元素应用样式的接口
* * DOM Traversal and Range: 定义了遍历和操作文档树的接口
* DOM3: 引入了以统一方式加载和保存文档的方法和验证文档的方法. DOM3 级也对 DOM 核心进行了扩展

基本也反应了一个HTML语言和CSS语言的发展阶段, 各个浏览器大厂在规范下做自己的实现.

> SVG,MathML,SMIL 都属于其他DOM标准,看浏览器的支持情况了.
 
<!-- more -->


### 扩展阅读:
DOM Event: [HTML DOM Events](https://www.w3schools.com/jsref/dom_obj_event.asp), 开头一段话非常好解释了什么事HTML DOM Events. Chrome浏览器的开发工程师就是围绕找DOM的几大方向,V8引擎,和一些开发调试工具,插件环境去做C/C++开发.

> HTML DOM Events 允许 JavaScript 注册不同的EventHandlers到元素上. Events通常结合函数一起使用, 函数本身不会马上执行直到事件被触发, 比如用户点击了一个元素或者拖拽了一个元素或者从一个input上blur了出来.
> Tip: The event model was standardized by the W3C in DOM Level 2. DOM Level 2 就是上文中提到的 DOM2

分为这么几大类

* Mouse Events
* Keyboard Events
* Frame/Object Events
* Form Events
* Drag Events
* Clipboard Events
* Print Events
* Touch Events
* ...更多

**每种 Event 都应对应的 的MouseEvent Object**,方便你能拿到具体的值.很多方法名都是on开头.

传统的写法
```html
<p id="demo" onclick="myFunction(this, 'red')">Click me to change my text color.</p>

<script>
function myFunction(elmnt,clr) {
    elmnt.style.color = clr;
}
</script>
```

使用EventListener,无需在html上面将DomEvent 和 JS function 做绑定.
这一直接在js中找了element然后添加event

```
document.getElementById("myBtn").addEventListener("click", function(){
    document.getElementById("demo").innerHTML = "Hello World";
});
```


## BOM - Browser Object Model

* window: 移动、缩放和关闭浏览器窗口的功能
* navigator: 浏览器的详细信息,浏览器名,系统平台,插件等
* location: 页面的URI的具体参数,协议,接口,query
* screen: 用户显示器分辨率详细信息
* history: 浏览历史和操作
* cookies: 后来又扩展出了 localStorage,PWA渐进等概念
* XMLHttpRequest: 网络请求和接受. Ajax的核心,IE当时搞了ActiveXObject

