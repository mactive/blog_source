title: iOS 朝花夕拾 -- 1 - Protocol
date: 2016-09-21 07:43:20
author: [mac.meng]
tags: [iOS,DawnBlossom,Series]
---
## TL;DR

> 断断续续做iOS开发也有5年时间了,中间不务正业的搞了2年JS.回过头来在看iOS的变化非常的快.虽然swift也在学习,面向protocol和struct编程嘛.但感觉Objective-C的很多知识点的深层次原理没有彻底搞懂. 收到业界一些大牛的启发和指导, 有人持续写刨根问底系列, 有人做架构对比分析, 微观的也好,宏观的也好. 本系列文章也想从自己的角度尝试理解一些问题, 将一些之前忽视的花朵重新拾起来品玩, 说不定有新的发现. 捎带写一些自己的感受, 希望让自己活得明白一些.

## 发明Protocol的原因

从 [Apple官方文档](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithProtocols/WorkingwithProtocols.html),[中文](http://wiki.jikexueyuan.com/project/programming-with-objective-c/working-with-protocols.html)可以得到的知识点.

### Protocol 本身是可以多重继承的
下面代码的含义是, 叫Mark的这个Protocol, 同时具备了 NSObject, NSCopying, NSCoding协议的内容. 因为,
1. `NSCopying` 协议有 `- copyWithZone:`方法,而且是@Required 的
2. `NSCoding` 协议有 `- initWithCoder:`和`- encodeWithCoder:`方法,也是@Required 的
3. `NSObject` 是类也是协议, 比较特殊. 而且没有必须要实现的协议方法.
所以, 所有实现MyProtocol协议的类,必须实现 上面这三个方法.

```
@protocol MyProtocol <NSObject, NSCopying, NSCoding>
...
@end
```

### 符合协议的实现的继承
* OC不像C++可以有多重继承,Objective-C是单一继承的, 如果想要做到一个类同时具有多种类的能力(属性和方法), 可以通过Protocol实现,弥补了OC语言在OOP上的缺陷

MyClass 需要实现 MyProtocol， AnotherProtocol， YetAnotherProtocol 这三个协议中标注必须要实现的方法. 但同时也享有了这三个协议中定义的 @porperty
```
@interface MyClass : NSObject <MyProtocol， AnotherProtocol， YetAnotherProtocol>
...
@end
```


### OC Protocol 和 Java Interface 可以简单类比
* OC -> @interface = Java -> Class
* OC -> @protocol = Java -> interface

## Protocol 的特性
* @required 所有实现这个协议的类,必须实现的方法
* @optional 可选实现

> 在Objective-C 2.0之後加了@required跟@optional的語法,有些OC1.0时代的代码没有这两个关键字,那么都是**必须要实现的**

举例,最常用的 `UITableViewDataSource`, rows必须要实现,但sections可以不用实现.

从 iOS 10.0/Frameworks/UIKit/UITableView.h  Line 297 节选
```
@protocol UITableViewDataSource<NSObject>

// 必须实现的
@required

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section;

// ...

// 可选实现的
@optional

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView;              // Default is 1 if not implemented

// ...
@end

```

## Protocol 的使用

![](/images/protocol_1.png)

* A.methodA 和 B.methodB 都是必须要实现的,因为他们有@required
* * 所以这个MyClass的类需要实现这些方法,
* * 他同时也获得了.name 属性,因为是readonly的, 所以需要自己实现get方法
* 这个delegate属性很有意思, 他是用Protocol B来定义的, 所以他具有了delgate的所有属性和方法.将delegate指向谁,谁就需要负责实现 @required 的方法, 具体实现也会打到哪里去.

> 使用Protocol相当于 把你在 `.h` 文件中 @interface 和 @end 之间的内容, 按照OOP的方式拆分到各处去.方便复用和继承, 都是是属性定义和方法定义
> 使用Category, 相当于 `.m`文件中的  @implementation 和 @end 之前的内容. 还可以获得方法的定义和实现.

PS: 但是Category不允许为已有的类添加新的属性或者成员变量。但是可以通过runtime.h中objc_getAssociatedObject / objc_setAssociatedObject [黑魔法实现](http://www.cnblogs.com/wupher/archive/2013/01/05/2845338.html)

### Gist完整例子代码
{% gist 0ddba230d8a79d470303111027628b1a MyClass.h %}


## 小结
* Protocol 只有一个文件 MyProtocol.h,他本身没有实现文件
* Protocol 对现实他的类有约束作用,必须实现,可选实现. 约束作用会通过继承**穿透**.
* Protocol 既可以约束类, 也可以作为属性定义变量
* Protocol 和 Category 是 Objective-C OOP的两大杀器

2011年出版的 [Pro Objective-C Design Patterns for iOS](https://www.amazon.com/Pro-Objective-C-Design-Patterns-iOS/dp/1430233303) 从未再版,但依然还很有深度, 本文也是对其中一个知识点的扩展. 作者在Apple工作多年, 至今还奋斗在Cocoa的前线. 

### 疑问
不知道最近Swift提到的 POP - 面向协议和结构体编程, 是不是跟这个Protocol有些关系, 但Swift应该有完整的OO的概念和FP的概念.

## 参考
* [Protocol in Objective-C](http://kaochenlong.com/2010/12/11/protocol-in-objective-c/)

* [Apple官方文档](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithProtocols/WorkingwithProtocols.html)

* [Objective-C——消息、Category和Protocol](http://www.cnblogs.com/chijianqiang/archive/2012/06/22/objc-category-protocol.html)


