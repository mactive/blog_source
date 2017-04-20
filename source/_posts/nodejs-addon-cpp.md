title: NodeJS 和 C++ -- 低(前)端程序员为什么要学C++
date: 2017-04-20 08:59:00
author: [mac.meng]
tags: [javascript,NodeJS,C++]
---

先来个简单的公式

### NodeJS = Foundation + V8 Engine + JS API

如果我们仔细看一下Node的源码,其中有23.8%的C++,5.6%的C,还有64.5%的JS.
`Foundation`中包含几大核心, File(文件操作),Network(网络),Event(事件驱动),Stream(流的概念),Module(模块系统). 这几块无一不是C/C++实现的.JS的解释器V8Engine更是C/C++实现的,别忘了Chrome可能是世界上最大的C/C++项目了.

`Foundtion`既然是C++实现的,那么在各个操作系统上的实现肯定不一样了.你可以想想一下读取Linux上的文件和Win10上的文件的C++代码肯定不一样, 而且你的C++代码需要调用操作系统的C++API才能实现功能. 据我所知,大部分的操作系统都是用C/C++写成的.

JS API可能是通用的,因为Node为我们实现了一套C->JS的bridge,这样Node开发组的同学就可以用JS做一些API的封装和抽象.而社区里写npm package的同学可以只使用JS API 就可以做一些工具包了.  因为`Foundation` 和 `V8Engine` 已经接受了多年业界的考验. 在此感谢Google和社区默默贡献的C/C++程序员.

<!-- more -->


同时Node开放了他们自己使用的Bridge,可以让社区也写一些[C/C++ Addon](https://nodejs.org/api/addons.html).提供了类似`NODE_SET_METHOD`,`NODE_MODULE`的C宏供开发的时候调用,官方给了一些实例教你如何在JS和C++之间传递Object和 Funciton,构造Function Factory,以及基本的数值转换.是不是感觉个ReactNative提供的Bridge非常的类似.

> 可能有同学有疑问,为什么有的时候NodeJS需要C++, 或者你是不是经常碰到`npm i`的时候特别慢.或者卡在node-gyp的什么地方. npm i 慢有两个原因一个是网速,一个是你在用你的电脑build C++代码.为啥要build C++,还是因为慢,你没能下载下来node-pre-gyp编译好的能在你系统上跑的C++二进制包(因为GFW),那就需要你本地打包. 可能有同学问为啥我需要C++呢, 比如因为存储,用到sqlite3,因为图形处理用到ffmpeg等等. 

下面我们讲讲node-gyp和他引出来的东西

## node-gyp 和 electron

![](/images/node_addon_1.png)

electron其实也是一个Framework,他把你开发桌面应用的可能用到东西帮你做好了,比如Menu(菜单),Window(窗体)等等.当然也需要社区帮你抹平各个操作系统上的差异.

* 拓展了sandbox(更多的系统操作的权限)
* 使得NodeJS不止做服务器端应用.而且可以做桌面应用.
* Make JS great again with standing on the C++'s shoulder

nw.js 是做这件事的先驱,不过现在不再继续维护,他两个可以说是一脉相承,都有人使用.


## C++ 或者其他编译型语言的过程

![](/images/node_addon_2.png)

静态语言都离不开这些步骤.在Compile的时候可能会因为你自己写的语法而报错.在Link的时候经常会找不到相应的库而不能执行下去. Debug和run的区别无非就是加入Symbol,让编译器在报错的时候能告诉你错误出在了哪一行,他的上下文之类的,方便你debug.你最后Release的版本必然是不包含symbol的. 一般一个可执行的Binary文件都会配一个symbol文件. 类似xx.min.js + xx.min.js.map

## tips

* chrome 50之后不再支持winXP, 应该跟XP上的C++执行器有关系.因为微软可能已经不再维护以WindowNT5为版本的winXP了.
* 如何你要做WinXP的客户端(比如我厂),那么你只能使用 nw.js <= 0.14.7的版本
> file addon.node

## github

* 这个文章配合的[github](https://github.com/mactive/my_node_addon)
* 结合使用 nan 和 binding 的 addon [github](https://github.com/nodejs/node-addon-examples)
* [NodeJS](https://github.com/nodejs/node)
* [node-gyp](https://github.com/nodejs/node-gyp)

## 推荐一些C++的学习资源

* [V8 文档](https://v8docs.nodesource.com/node-7.4/) 配合 [C/C++ Addon](https://nodejs.org/api/addons.html) 来看
* [C++中文菜鸟教程](http://www.runoob.com/cplusplus/cpp-tutorial.html)
* www.learncpp.com(http://www.learncpp.com/)



