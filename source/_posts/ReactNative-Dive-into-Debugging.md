title: 深入理解 React Native Debugging
date: 2016-06-23 19:03:36
author: [mac.meng]
tags: [ReactNative,Debugging,Dive,翻译]
---

> 翻译改编自 Shaheen Ghiassy的[Deep Diving React Native Debugging](https://medium.com/@shaheenghiassy/deep-diving-react-native-debugging-ea406ed3a691#.hudt9frpw) from medium.com

**中文译名**: 深入理解 React Native Debugging

译者注: 使用React Native(以下简称RN) 开发已经有3个月了,Debug更是每天都会用到的技能.最近闲来看看一些原理性的文章,决定先从debug入手,可以更深入了解到框架背后的东西.原作者写这篇文章的时候RN还是0.11版本,我的开发版本是 0.20.其实debug的原理没有改变,只是个别环节的实现function增加了一些参数.下面我们开始正文

——————

如果你和我一样,当你看到React Native 简单的debugging的时候,你一定会觉得这太拽了.在Chrome浏览器里打下断点,就可以终端iOS应用.而且可以console和watch各种变量.

但如果你和我一样,你一定不会相信编程中有魔法的存在,而要去探究他的工程实现.这篇文章就是带你理解React Native Debugging 背后的原理.

<!-- more -->


tl;dr 文章很长,不喜勿读

三方清单

* 一个NodeJS server. 称为 Packager. 运行在 terminal中
* Chrome 浏览器从Packager加载你的React Native JavaScript,并提供内置的debugging  支持,就像 debug 普通的JavaScript代码一样
* 设备上跑的App 和 在浏览器中跑的js应用 通过webSocket 通信. 设备和浏览器互相通过JSON命令通讯,浏览器来控制设备. 当然站在浏览器背后的是这个 Packager.

下面就来详细讲述这三方的关系.

## 深入理解

如果你想继续了解,你会对和debugging有关的过程,文件和操作更感兴趣.

> Note: React Native 发展迅速, 本篇文章基于 React Native 0.20 版本

让我们先来直观的感受一下上面提到的三方+你的代码

![一个典型的react应用中的各个角色](https://cloud.githubusercontent.com/assets/265410/16294974/7ee42dfc-3956-11e6-8478-2eb8a46cf6c0.png)

这四方之前的关系图有点小复杂,我们会慢慢展开.Facebook和一些第三方都在致力于开发一站式的开发工具,例如[Nuclide IDE](http://nuclide.io/), [Deco Sofeware](https://www.decosoftware.com/),但无论怎么说,我们还是探究一下各方的细节.

**A. 你的移动设备**: native code 运行在上面
**B. NodeJS Server**: Facebook’s [Packager](https://github.com/facebook/react-native/tree/master/packager) NodeJS Server. 一个类WebPack的项目,有个基于 CommonJS 的模块系统, 和一堆可用的黑科技.用来帮助 React Native 开发
**C. Chrome 浏览器**: 这个不用介绍了吧
**D. React Native JavaScript code**: 你的价值体现

## 那么究竟在 Debugging 时发生了什么

### Step 1: Start Packager

如果你在命令行中输入: “npm start”. 那么他会触发 react-native/local-cli/cli.js 去执行start.这个在 项目的package.json 中可以看到. 包括在XCode > Build Phases > React-Native shell > react-native-xcode.sh 最后执行的也是 cli.js. 然后他会起一个 NodeJS Server,基于 Connect 开发的.

{% gist e3c8f76cd59a26d5d8353b8542374ac1 runServer.js %}

https://gist.github.com/mactive/e3c8f76cd59a26d5d8353b8542374ac1

顺序是这样的

1. Packager 打包好并host http://localhost:8081/index.ios.bundle
2. packager 触发 http://localhost:8081/launch-chrome-devtools
3. chrome打开 http://localhost:8081/debugger-ui.html

![Terminal 中的呈现](https://cloud.githubusercontent.com/assets/265410/16288966/ca30c06e-3923-11e6-90d1-05218ad813ce.png)

同时 Packager 会配置一个 WebSocket 的服务,这个稍后介绍.
> **tips:** webSocket 是附着在http协议上的. http://localhost:8081 ws://localhost:8081

### Step 2: Run React Native App in Simulator

在XCode中 run,然后模拟器中会呈现. 实测是这样的, 跳过Step 1.直接在Xcode中run,系统会自动默认的Terminal,然后跑 npm start. 在真机调试一样也会启动.

![程序跑起来了](https://cloud.githubusercontent.com/assets/265410/16295885/f5b6dfa6-395b-11e6-8c3e-07f4788bce3b.png)

> 真机调试 Tip: 在用真机的时候,ws 服务的地址要换成 笔记本的IP,不能用localhost
要不iOS设备在Wifi环境下访问不到 ws服务. [Automated IP Configuration for React Native Development](http://moduscreate.com/automated-ip-configuration-for-react-native-development/)

一旦App在设备上跑起来,他会去访问 http://localhost.com:8081/index.ios.bundle,并把它转换成原生的代码执行. 想看React Native原理可以参考[React Native通信机制详解](http://blog.cnbang.net/tech/2698/)和[React Native之底层源码分析篇](http://blog.csdn.net/u014486880/article/details/51535391)

Packager 是跑在本地的,一旦他收到浏览器或者App的请求,他会使用Babel和Facebook自家的grapher 去collect,concat,transplie and modularize,你的 React Native JavaScript (D) 代码编程一个 response. 所以当你改动代码并保存的时候你会看到 Terminal 中会重新 transforming.

![Packager process](https://cloud.githubusercontent.com/assets/265410/16297580/0281766c-3964-11e6-96fe-d3653564ce0d.png)


> 访问 [http://localhost:8081/index.ios.bundle](http://localhost:8081/index.ios.bundle),看一下package之后的代码

当iOS设备收到代码的时候, 它会把JavaScript 代码放到 Apple’s JavaScript Code 中去执行.


### Step 3: Turn on Debugging Mode

现在App在设备上正常的跑着,下一步就是进入 debug 模式,这样就可以通过Chrome的webSocket inspector 去查看 React Native 编译过的版本 和 未编译版本 之前传输的数据了.

![Chrome webSocket inspector](https://cloud.githubusercontent.com/assets/265410/16297760/ca859d0a-3964-11e6-9215-fa6a1722c20e.png)

在模拟器上按 Command+D.会弹出调试选项.真机调试的时候摇一摇也会出现

![React Native’s on-screen Developer menu](https://cloud.githubusercontent.com/assets/265410/16298920/39de43e6-396a-11e6-8f7a-1e19ad81513d.png)

当你点击Debug in Chrome 的时候,客户端会发起一个请求 [http://localhost:8081/launch-chrome-devtools](http://localhost:8081/launch-chrome-devtools).然后Packager会接到这个请求,然后通过 [opn](https://github.com/sindresorhus/opn)执行两个操作.

第一: 打开Chrome 的一个tab, 第二: 让这个tab打开URL [http://localhost:8081/debugger-ui](http://localhost:8081/debugger-ui)

![Debug in Chrome process](https://cloud.githubusercontent.com/assets/265410/16299385/a19bdc76-396c-11e6-8c46-30a4d4a3d4ae.png)

### Step 4: Debugger-ui.html and Device establish a connection

现在我们有了一个Chrome Tab,url 是 http://localhost:8081/debugger-ui.这个页面的静态文件是debugger-ui.html.这个网页会有一个请求里通过 websocket 和 Packager 保持通讯.

{% gist 47fd28ec51037026ae4364cdcf633cbb debugger.html.js %}

https://gist.github.com/mactive/47fd28ec51037026ae4364cdcf633cbb

你的App会发送3个WebSocket ping到你的Chrome. 如果Chrome反馈给你 expectedId/sessionId,那么链接将会马上建立.否则设备会抛出一个红屏”Runtime is not ready”.相信做过React Native 开发的对这个红屏不会陌生.一般 Command+R 一下就可以解决.你的App和浏览器之前的交互中间都会经过 NodeJSServer.因为http和ws服务都是他运行的.

### Step 5: Execute Application Script

链接建立之后.App将会发送 WebSocket 消息到浏览器,去加载 JavaScript code.

{% gist 3cb2e2c53c3b9edf48a0793dae3c82cf RCTWebSocketExecutor.m %}

https://gist.github.com/mactive/3cb2e2c53c3b9edf48a0793dae3c82cf

发送的这个消息非常有意思,发送的时候回注入一个字典.字典中有很多 module/method/id 的映射关系.这里的module/method 就对应RCTModule 和 RCTMethod. 这个字典在chrome中也有体现(下图).一个真实项目中的字典可以参见附录A

这个启动消息被运行在Chrome的debugger-ui接受到.消息被插接之后放入Chrome的window对象的“__fbBatchedBridgeConfig”属性中.

![chrome 中的 window.__fbBatchedBridgeConfig](https://cloud.githubusercontent.com/assets/265410/16288993/0aa47d02-3924-11e6-9f01-b56487ca39f6.png)

{% gist c17a2dca25b3711e32c713a9f0006e9b debuggerWorker.js %}

https://gist.github.com/mactive/c17a2dca25b3711e32c713a9f0006e9b

当Chrome的window对象更新完之后.浏览器会加载 bundlejs.
然后debuggerWorker 会吧bundlejs+sourcemap 再还原成一个一个的文件供我们debug,在chrome中可以用 Command+P 来定位文件

* bundlejs 后的文件: http://localhost:8081/index.ios.bundle
* sourcemap:  http://localhost:8081/index.ios.map?platform=ios&dev=true

![debuggerWorker sourcemap](https://cloud.githubusercontent.com/assets/265410/16288980/e6812830-3923-11e6-93ed-b5e34a5f95d2.png)


![Execute flow](https://cloud.githubusercontent.com/assets/265410/16300546/0ce516cc-3973-11e6-965b-64fc3dc320b0.png)

从上图可以看出,App和Chrome之间的交互离不开Packager,最后Chrome中的网页会告知executeApplicationScript webSocket消息已经发出


### Step 6: Run

当 executeApplicationScript 被设备成功接受到之后.App就会重新加载js,呈现给我们.感觉Chrome和App之间通过WebSocket通信,屌屌的. 至此你可以在Chrome像调试网页的JS一样调试ReactNative的js了.不过请注意很多第三方node_module的东西无法console出来.就算是在下断点的时候.比如lodash,具体原因不清.


## 结语

令人值得称道的一点是, React Native js在Chrome中的运行方式和 React Native js在设备上 JavaScriptCore 的运行方式很像.都是有个 Module和method的巨大的映射表.

例如 window.__fbBatchedBridgeConfig 在设备上也有类似的mapping.这个相似点很棒.这给我们提供可一种观察JavaScriptCore的方案.

希望本文能够帮你更好的理解 React Native, 并写出更好的应用.

## 附录A


{% gist 1b90cf35e473f438f91a message.inject.__fbBatchedBridgeConfig.json %}

https://gist.github.com/sghiassy/1b90cf35e473f438f91a#file-message-inject-__fbbatchedbridgeconfig-json


接下来会继续翻译一下这个作者的其他文章,以及更多的原理方面的文章.

