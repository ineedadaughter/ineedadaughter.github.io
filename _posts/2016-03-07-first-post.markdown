---
layout: post
title:  "web交互"
date:   2014-08-31 14:36:23
---

分享一个调用js代码监听webView上的图片，超链接点击效果
	
	- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType{

    

		NSString *requestString = [[request URL]  absoluteString];

    

    if ([requestString  hasPrefix:@"myweb:imageClick:"]) {

        

        NSString *imageUrl = [requestString  substringFromIndex:@"myweb:imageClick:".length];

        

        NSLog(@"image url------%@", imageUrl);

        

        //创建视图并显示图片

        

        [self showBigImage:imageUrl];

        

        return NO;

        

    }

    

    if ([requestString  hasPrefix:@"myweb:URLClick:"]) {

        

        NSString*url=[requestString  substringFromIndex:@"myweb:URLClick:".length];

        

        NSLog(@"url-------%@",url);

        

        BannerWebViewController *webVC = [[BannerWebViewController alloc]  init];

        

        webVC.url = url;

        

        [self.navigationController pushViewController:webVC animated:YES];

        

    }

    

    return  YES;

     }
 


    - (void)webViewDidFinishLoad:(UIWebView *)webView{

   

    static   NSString * const jsGetImages =

    @"function getImages(){\

    var objs = document.getElementsByTagName(\"img\");\

    for(var i=0;i<objs.length;i++){\

    objs[i].onclick=function(){\

    document.location=\"myweb:imageClick:\"+this.src;\

    };\

    };\

    return objs.length;\

    };";

    

    [webView stringByEvaluatingJavaScriptFromString:jsGetImages];

    //注入js方法

    

    //注入自定义的js方法

    [webView stringByEvaluatingJavaScriptFromString:@"getImages()"];

    

    

    

    static  NSString * const jsGetURls =

    @"function getUrls(){\

    var objs = document.getElementsByTagName(\"a\");\

    for(var i=0;i<objs.length;i++){\

    objs[i].onclick=function(){\

    document.location=\"myweb:URLClick:\"+this.href;\

    };\

    };\

    return objs.length;\

    };";

    

    

    [webView stringByEvaluatingJavaScriptFromString:jsGetURls];

    

    [webView stringByEvaluatingJavaScriptFromString:@"getUrls()"];

    

}
