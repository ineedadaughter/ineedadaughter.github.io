---
layout: post
title:  "web交互"
date:   2016-03-31 12:36:23
permalink: /first-post.html
---

分享一个调用js代码监听webView上的图片，超链接点击效果：

	//截获图片点击方法
    static  NSString * const jsGetImages =
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


    //截获超链接点击方法

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