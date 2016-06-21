title: Delegate passvalue in iOS
date: 2012-11-11 16:03:15
author: [mac.meng]
tags: [iOS,delegate]
---

#delegate 传值实例

实现一个delegate
### @PassValueDelegate

	#import <Foundation/Foundation.h>

	@protocol PassValueDelegate <NSObject>

	@optional
	-(void)passStringValue:(NSString *)value andIndex:(NSUInteger )index;
	-(void)passNSDateValue:(NSDate *)value andIndex:(NSUInteger)index;

	@end

###@ ProfileMeController

谁需要实现delegate的方法 就在interface的时候写上 

	@interface ProfileMeController : UIViewController<PassValueDelegate>{}

然后在implation 中写具体实现
在初始化EditViewController的时候 需要

####controller.delegate = self;


	-(void)passStringValue:(NSString *)value andIndex:(NSUInteger )index;
	{
   		NSLog(@"*** %@ %d ***",value,index);
   	}
   	
##### 这里的delegate实现是为了 从 EditViewController 向 ProfileMeController 传递值,编辑完信息然后传回来


###@ EditViewController

interface的时候不用写delegate 但是 要声明一个变量

	@interface EditViewController : UIViewController{}
	
	@property(nonatomic,assign) NSObject<PassValueDelegate> *delegate;
	
然后在需要的地方传值

	[self.delegate passStringValue:self.valueTextField.text andIndex:self.valueIndex];
	
##### 另外不要忘记在 edit和profile 这两个文件的头部引用 PassValueDelegate.h