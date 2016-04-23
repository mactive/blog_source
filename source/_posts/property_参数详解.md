title: property 参数详解
date: 2013-11-06 11:07:44
tags:
---
###property 参数详解


	@property(nonatomic/atomic, readwrite/readonly, strong/weak/assign)


####readonly / readwrite 可读性：

readonly 只有get方法
readwrite set和get 方法都有

####atomic / nonatomic

atomic 并不代表线程安全，只是说对同一对象的set和get的操作是顺序执行的

nonatomic 不保证 set和get 方法是按照顺序执行的,但速度比atomic快


####strong/weak/assign/copy

ARC 下再不用 retain 只用strong

如果想用 weak, weak references to Objective-C objects.

	@property(nonatomic, weak)NSString *name;

如果想用 assign for C primitive properties,  NSInteger, int, float 等等

	@property(nonatomic, assign)int name;

如果想用copy

摘自Clang ARC 方法[ARC是支持copy的]

copy implies __strong ownership, as well as the usual behavior of copy semantics on the setter.

So if you implement custom setters, you're responsible for implementing strong or weak semantics in those setters.

copy意味着strong,而且通常会copy setter方法.如果你实现自己定制的setter方法,请使用strong或者weak方法. 所以一般NSNumber 或者 NSString使用 copy,因为他们一般不用自定义setter方法

	@property (nonatomic, copy) NSNumber *userID;
	@property (nonatomic, copy) NSString *username;
