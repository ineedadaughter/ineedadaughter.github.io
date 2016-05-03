---
layout: post
title:  "随手指滑动绘图+截屏保存相册"
date:   2016-04-10 14:36:23
---

	//随手指滑动绘图
	#import <UIKit/UIKit.h>


	@interface DoodleView : UIView {
    CGContextRef context;
    CGLayerRef layer;
    float brushWidth;
    float brushColor;
	}

	@end



	#import "DoodleView.h"


	@implementation DoodleView

	- (id)initWithFrame:(CGRect)frame
	{
    self = [super initWithFrame:frame];
    if (self) {
        // Initialization code
        self.backgroundColor = [UIColor whiteColor];
        
        CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
        context = CGBitmapContextCreate(NULL, self.frame.size.width, self.frame.size.height, 8, 4 *self.frame.size.width, colorSpace, kCGImageAlphaPremultipliedFirst);
        CGColorSpaceRelease(colorSpace);
        
        layer = CGLayerCreateWithContext(context, self.frame.size, NULL);
        CGContextRef layerContext = CGLayerGetContext(layer);
        brushWidth = 5.0;
        CGContextSetLineWidth(layerContext, brushWidth);
        CGContextSetLineCap(layerContext, kCGLineCapRound);
        brushColor = 0.0;
        CGContextSetRGBStrokeColor(layerContext, brushColor, brushColor, brushColor, 1);
    }
    return self;
	}

	// Only override drawRect: if you perform custom drawing.
	// An empty implementation adversely affects performance during animation.
	- (void)drawRect:(CGRect)rect
	{
    CGContextRef currentContext = UIGraphicsGetCurrentContext();
    CGImageRef imageRef = CGBitmapContextCreateImage(context);
    CGContextDrawImage(currentContext, [self bounds], imageRef);
    CGImageRelease(imageRef);
    CGContextDrawLayerInRect(currentContext, [self bounds], layer);
	}

	- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    UITouch *touch = [touches anyObject];
    if ([touch tapCount] == 2) {
        CGContextClearRect(context, [self bounds]);
        [self setNeedsDisplay];
    } else {
        [self touchesMoved:touches withEvent:event];
    }
	}

	- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event {
    UITouch *touch = [touches anyObject];
    CGPoint location = [touch locationInView:self];
    CGPoint pastLocation = [touch previousLocationInView:self];
    
    CGContextRef layerContext = CGLayerGetContext(layer);
    CGContextBeginPath(layerContext);
    CGContextMoveToPoint(layerContext, pastLocation.x, pastLocation.y);
    CGContextAddLineToPoint(layerContext, location.x, location.y);
    CGContextStrokePath(layerContext);
    
    [self setNeedsDisplay];
	}

	- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event {
    CGContextDrawLayerInRect(context, [self bounds], layer);
    CGContextClearRect(context, [self bounds]);
	}

	- (void)dealloc
	{
    CGContextRelease(context);
    CGLayerRelease(layer);
    [super dealloc];
	}

	@end




	//截图 截取屏幕 
	    // 从view上截图  
    - (UIImage *)getImage {  
          
        UIGraphicsBeginImageContextWithOptions(CGSizeMake(150, 150), NO, 1.0);  //NO，YES 控制是否透明  
        [self.view.layer renderInContext:UIGraphicsGetCurrentContext()];  
        UIImage *image = UIGraphicsGetImageFromCurrentImageContext();  
        UIGraphicsEndImageContext();  
        // 生成后的image  
          
        return image;  
    }  
      
    // 根据给定得图片，从其指定区域截取一张新得图片  
    -(UIImage *)getImageFromImage{  
        //大图bigImage  
        //定义myImageRect，截图的区域  
        CGRect myImageRect = CGRectMake(70, 10, 150, 150);  
        UIImage* bigImage= [UIImage imageNamed:@"mm.jpg"];  
        CGImageRef imageRef = bigImage.CGImage;  
        CGImageRef subImageRef = CGImageCreateWithImageInRect(imageRef, myImageRect);  
        CGSize size;  
        size.width = 150;  
        size.height = 150;  
        UIGraphicsBeginImageContext(size);  
        CGContextRef context = UIGraphicsGetCurrentContext();  
        CGContextDrawImage(context, myImageRect, subImageRef);  
        UIImage* smallImage = [UIImage imageWithCGImage:subImageRef];  
        UIGraphicsEndImageContext();  
        return smallImage;  
    }  