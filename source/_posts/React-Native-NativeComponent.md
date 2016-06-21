title: ReactNative-NativeComponent -- 1. 官方指引
date: 2016-03-31 12:03:15
author: [mac.meng]
tags: [Objective-C,ReactNative,Component]
---
## Native-Module 基本分为两种
* 可见的Module. (显示,原生动画)
View 本身继承自RCTView,如果想要在JS中使用,需要再实现一个 Manager
* 不可见的Module. (数据,网络,modeling)
需要实现 <RCTBridgeModule>delegate

今天我们来讲讲第二种不可见的Module

### Module 和 method的关系
#### One Module
> -> MethodA
> -> MethodB
> …
> -> MethodN
 
### [官方指引](http://facebook.github.io/react-native/docs/native-modules-ios.html#content)

1. 正常的参数, 所有json格式的参数
而且有个强大的Util RCTConvert.h 从字体,颜色,string,number都可以转换
2. 支持回调 callback, 可以传function进去 
原生类型是 RCTResponseSenderBlock
和传参都是 用RCT_EXPORT_METHOD 声明
3. 支持Promise
但是需要用到 RCT_REMAP_METHOD 声明,再辅助两个block 
RCTPromiseResolveBlock/ RCTPromiseRejectBlock
JS 层面 用  ES2016 **async/await** 去写, 这个需要看一下

4. 接下来讲多线程 
methodQueue 是在一个Module下的所有method共享的, 要么这个module是在主线程上执行,要么就是在某个线程上执行. 而且大家可以共享这个线程
如果我有一个方法需要单独在新的线程上执行怎么办,比如相对耗时的存储等等.我可以在方法里面 用dispatch_async包一下执行代码,执行完之后可以用 2.里面提到的callback通知外面
最后提到了 在不同的Module中共享线程,比较高深

5. 导出常量
返回Dictionary的native method 在初始化的时候完成赋值,运行时的时候再改变他,js也拿不到最新的值.
如果要枚举常量,他建议新建一个RCTConvert的extension
6. 向JS发送Event
如何使用 bridge.eventDispatcher, 如何在native send 以及如何在JS subscription. 这样Native 也可作用在JS上了,这是除了 callback的另一种实现.
采用的是观察者模式. 如果想用JS作用在Native上直接调取Native 用RCT_EXPORT_METHOD 暴露出来的函数就好了,各种回调,promise,sender伺候你.

这里多数一句,我们在OC里常用的传递消息的方式有很多 KVO,Delegate,Block. 还有高逼格的NSOperation,GCD 等等.基本上RN都用了.可见facebook的原生功力还是比较深厚的

7. 最后提到了如何在swift 用RN,然并卵.

### RN = ReactNative

————

#### 有兴趣的同学可以读一下具体 Module 和 Method 的原生实现
> 关于Bridge的一篇facebook的[内部剖析](http://tadeuzagallo.com/blog/react-native-bridge/)
> 如何优化RNApp性能的[官方介绍](https://code.facebook.com/posts/895897210527114/dive-into-react-native-performance/)

#### next
下一部分准备按照官方的指引写一个Demo 把这些点传起来

