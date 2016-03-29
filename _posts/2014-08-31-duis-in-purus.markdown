---
layout: post
title:  "关于ios的一些奇怪的问题"
date:   2016-03-07 14:36:23
categories: Duis
---
1.使用asi的老版本框架，request并没有经过释放处理，为避免崩溃

	 - (void)dealloc  
	{

	[request setDelegate:nil];

	[request cancel];

	}


2.判断是否为ipad上运行

	#define isPad (UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad)


3.能不能只用一个pan手势来代替UISwipegesture的各个方向？

	 - (void)pan:(UIPanGestureRecognizer *)sender  
	{  
	typedef NS_ENUM(NSUInteger, UIPanGestureRecognizerDirection) {  
	    UIPanGestureRecognizerDirectionUndefined,  
	    UIPanGestureRecognizerDirectionUp,  
	    UIPanGestureRecognizerDirectionDown,  
	    UIPanGestureRecognizerDirectionLeft,  
	    UIPanGestureRecognizerDirectionRight  
	};  
	static UIPanGestureRecognizerDirection direction = UIPanGestureRecognizerDirectionUndefined;  
	switch (sender.state) {  
	    case UIGestureRecognizerStateBegan: {  
	        if (direction == UIPanGestureRecognizerDirectionUndefined) {  
	            CGPoint velocity = [sender velocityInView:recognizer.view];  
	            BOOL isVerticalGesture = fabs(velocity.y) > fabs(velocity.x);  
	            if (isVerticalGesture) {  
	                if (velocity.y > 0) {  
	                    direction = UIPanGestureRecognizerDirectionDown;  
	                } else {  
	                    direction = UIPanGestureRecognizerDirectionUp;  
	                }  
	            }  
	            else {  
	                if (velocity.x > 0) {  
	                    direction = UIPanGestureRecognizerDirectionRight;  
	                } else {  
	                    direction = UIPanGestureRecognizerDirectionLeft;  
	                }  
	            }  
	        }  
	        break;  
	    }  
	    case UIGestureRecognizerStateChanged: {  
	        switch (direction) {  
	            case UIPanGestureRecognizerDirectionUp: {  
	                [self handleUpwardsGesture:sender];  
	                break;  
	            }  
	            case UIPanGestureRecognizerDirectionDown: {  
	                [self handleDownwardsGesture:sender];  
	                break;  
	            }  
	            case UIPanGestureRecognizerDirectionLeft: {  
	                [self handleLeftGesture:sender];  
	                break;  
	            }  
	            case UIPanGestureRecognizerDirectionRight: {  
	                [self handleRightGesture:sender];  
	                break;  
	            }  
	            default: {  
	                break;  
	            }  
	        }  
	        break;  
	    }  
	    case UIGestureRecognizerStateEnded: {  
	        direction = UIPanGestureRecognizerDirectionUndefined;     
	        break;  
	    }  
	    default:  
	        break;  
	}  
	} 


4.ScrollView莫名其妙不能在viewController划到顶怎么办？

	self.automaticallyAdjustsScrollViewInsets = NO;


5.怎么像Safari一样滑动的时候隐藏navigationbar？  	

	navigationController.hidesBarsOnSwipe = Yes; 


6.项目没有继承baseViewController，但是现在每个ViewController都要执行一些方法，现在要改怎么办？

	使用<objc/message.h>方法交换啊，骚年.


7.array含有不同类型的model时需要排序怎么办？

	使用协议属性啊，小兄弟.


8.如何优雅的退出应用程序，就像点击home键一样流畅？

	不使用
	exit(0);
	也不用
	[[UIApplication sharedApplication] terminateWithSuccess];
	请叫我雷锋（私有方法，慎用）：
	[[UIApplication sharedApplication] performSelector:@select(suspend)];


9.你玩过iMessage么，滑动时的动画是不是印象深刻？

喜欢吗？[看这里](https://github.com/terryworona/messages-ios)。


10.ios私有api大全？

你值得拥有，suspend就是我在私有api大全上找到的方法[iphone-private-frameworks](https://github.com/kennytm/iphone-private-frameworks/tree/master) 酷不酷？想不想学？


11.延长启动时间，让启动页停留更久一点吧

	在appdelegate中加载window时添加：
	[NSThread sleepForTimeInterval:3.0];

write the code , change the world!