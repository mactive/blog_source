title: property 参数详解
date: 2013-11-06 11:07:44
author: [mac.meng]
tags: [Objective-C, Memory]
---
# 关于@property特性的介绍

	@property(nonatomic/atomic, readwrite/readonly, strong/weak/assign)

@Property是声明属性的语法，它可以快速方便的为实例变量创建存取器，并允许我们通过点语法使用存取器。
每个@property属性都含有特性（attribute），用来描述响应存取方法的行为，这些特性需要写在小括号里。任何@property属性都有特性，每个特性都有不同的可选类型，每种特性中都有一项是默认的。如果使用默认特性则不用在括号中表明。

### 1.多线程特性
多线程特性有两种可选类型：nonatomic 和 atomic，其中后者为默认特性。但因atomic这种线程保护技术很耗费资源，在iOS开发中一般使用nonatomic特性。

atomic：默认是有该属性的，这个属性是为了保证程序在多线程情况，编译器会自动生成一些互斥加锁代码，避免该变量的读写不同步问题。他并不代表线程安全，只是说对同一对象的set和get的操作是顺序执行的
nonatomic：如果该对象无需考虑多线程的情况，请加入这个属性，这样会让编译器少生成一些互斥加锁代码，可以提高效率。不保证 set和get 方法是按照顺序执行的,但速度比atomic快

### 2.读/写特性
readwrite、readonly这两个属性的价值，不是提供成员变量访问接口，而是控制成员变量的访问权限。

readwrite：这个属性是默认的情况，会自动为你生成存取器。
readonly：只生成getter不会有setter方法。

### 3.内存管理特性
内存管理特性的类型可以决定相应的实例变量将如何引用对象。

> ARC 下再不用 retain 只用strong

**assign**：默认类型，setter方法直接赋值，不进行任何retain操作，不改变引用计数。该方法只会针对“纯量类型”(CGFloat或NSInteger等)和C数据类型（int, float, double, char, 等等）的简单赋值操作，id类型也要用assign，所以一般iOS中的代理delegate属性都会用assign来标示。 如果想用 assign for C primitive properties,  NSInteger, int, float 等等

	@property(nonatomic, assign)int name;

**strong**：强引用，其存亡直接决定了所指向对象的存亡。使用该特性实例变量在赋值时，会释放旧值同时设置新值，对对象产生一个强引用，即引用计数+1。如果不存在指向一个对象的引用，并且此对象不再显示在列表中，则此对象会被从内存中释放。
**weak**：弱引用，不决定对象的存亡。属性表明了一种”非拥有关系“，既不释放旧值，也不保留新值，即引用计数不变，当指向的对象被释放时，该属性自动被设置为nil。即使一个对象被持有无数个弱引用，只要没有强引用指向它，那么还是会被清除。

如果想用 weak, weak references to Objective-C objects.

	@property(nonatomic, weak)NSString *name;

**copy**：和strong类似，不过该属性会被复制一个新的副本。很多时使用copy是为了方式Mutable（可变类型）在我们不知道的情况下修改了属性值，而用copy可以生成一个不可变的副本防止被修改。如果我们自己实现setter方法的话，需要手动copy。用 @property 声明 NSString、NSArray、NSDictionary 经常使用 copy 关键字，是因为他们有对应的可变类型：NSMutableString、NSMutableArray、NSMutableDictionary，他们之间可能进行赋值操作，为确保对象中的字符串值不会无意间变动，应该在设置新属性值时拷贝一份。

摘自Clang ARC 方法[ARC是支持copy的]
copy implies __strong ownership, as well as the usual behavior of copy semantics on the setter.

So if you implement custom setters, you're responsible for implementing strong or weak semantics in those setters.

copy意味着strong,而且通常会copy setter方法.如果你实现自己定制的setter方法,请使用strong或者weak方法. 所以一般NSNumber 或者 NSString使用 copy,因为他们一般不用自定义setter方法

	@property (nonatomic, copy) NSNumber *userID;
	@property (nonatomic, copy) NSString *username;


**unsafe_unretained**：和weak一样，唯一的区别就是当对象被释放后，该属性不会被设置为nil。所以是unsafe的。

### 4.方法名特性
方法名可以修改为我们合成的方法名，可以使存取方法语义更加符合应用场景。