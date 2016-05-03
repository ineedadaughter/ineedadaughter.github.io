---
layout: post
title:  " iOS URL Scheme劫持-在未越狱的iPhone 6(iOS 8.2)上盗取支付宝和微信支付的帐号密码（转）"
date:   2016-04-11 14:36:23
---
事先声明：1、该漏洞是iOS系统漏洞，和支付宝，微信app无关。本文只是拿支付宝和微信作为演示漏洞的应用，其他应用同样可以中招，转发者请勿断章取义。2、此漏洞是另一个漏洞，和“在非越狱的iPhone 6 (iOS 8.1.3) 上进行钓鱼攻击 (盗取App Store密码)”上利用的漏洞无关，本人不会干用一个漏洞写两个文章灌水的事情。

 

该漏洞最早是由我在FireEye的同事hui, Songjin和lenx发现的，因为该漏洞利用简单，修复却非常复杂，所以在iOS 8.2上还是未能修复。虽然iOS尚未修复，但app本身还是可以有防护的方法，本人在文章最后会提出一些应急的解决方案以供开发人员参考。

 

首先来看demo:

在未越狱的iPhone6(iOS 8.2)上盗取支付宝帐号密码

Youtube（需翻墙）: [https://www.youtube.com/watch?v=p86Kv1uHO-s](https://www.youtube.com/watch?v=p86Kv1uHO-s)

腾讯视频: [http://v.qq.com/page/p/d/s/p0149z3tgds.html](http://v.qq.com/page/p/d/s/p0149z3tgds.html)

 

在未越狱的iPhone6(iOS 8.2)上盗取微信支付密码

Youtube（需翻墙）: [https://www.youtube.com/watch?v=4SfCVGReyKI](https://www.youtube.com/watch?v=4SfCVGReyKI)

腾讯视频: [http://v.qq.com/page/i/f/h/i0149uh43fh.html](http://v.qq.com/page/i/f/h/i0149uh43fh.html)

 

DEMO细节分析（支付宝）：

 

在iOS上，一个应用可以将其自身”绑定”到一个自定义 URL Scheme 上，该 scheme 用于从浏览器或其他应用中启动该应用。这个设计非常类似于Android上的broadcast和broadcast receiver，但远远没有android上的复杂。美团利用支付宝付款的整个过程如图一所示：美团首先将订单信息通过URL Scheme发送给Alipay，Alipay收到订单信息，调用支付界面，用户在Alipay上完成支付后，Alipay再发送一个URL Scheme给美团，美团收到付款信息后，显示团购成功的界面。

 ![](http://i.imgur.com/XvuVpLd.png)

图一、正常支付流程

 

但因为URL scheme这个机制太简单了，完全没有考虑有多个app声明同一个URL Scheme的情况，也没有权限管理之类的方案。在iOS官方说明中：“在多个应用程序注册了同一种URL Scheme 的时候，iOS 系统程序的优先级高于第三方开发程序。但是如果一种URL Scheme 的注册应用程序都是第三方开发的，那么这些程序的优先级关系是不确定的。”实际上，经过我们的测试，这个顺序是和Bundle ID有关的，如果精心构造Bundle ID，iOS总是会调用我们app的URL Scheme去接收相应的URL Scheme请求。那么问题来了，如果我们精心构造一个app并声明“alipay”这个URL Scheme会怎么样呢？

 

结果就如demo中所演示的那样，后安装的FakeAlipay应用劫持了美团与支付宝之间的支付流程，并且可以在用户毫无意识情况下获取用户的帐号，支付密码，以及帮用户完成支付。

整个过程如图二所示：FakeAlipay在收到美团发来的订单信息后，构造了一个和支付宝一样的登陆界面，用户在输入了帐号密码后，FakeAlipay会把帐号密码以及订单信息发送到黑客的服务器上，黑客在获得了这些信息后，可以在自己的iOS设备上完成支付，并把支付成功的URL Scheme信息发回给FakeAlipay，FakeAlipay再把支付成功的URL Scheme信息转发给美团。因为时间原因，demo做得比较粗糙，没有做转发信息给美团这一部分的演示，但绝对是可行的。

 ![](http://i.imgur.com/l42AZ6H.png)

图二、劫持后的支付流程

 

这种攻击可以成功的原因除了iOS本身的漏洞外，支付宝也有一定的责任。那就是发给支付宝的订单信息并不是绑定当前设备的。因为这个原因，黑客可以在其他的iOS设备上完成支付。同样是因为不绑定当前设备的问题，黑客甚至可以先在自己的设备上生成好订单，然后在用户打开支付宝支付的时候把订单替换掉，让用户给黑客的订单买单。

 

DEMO细节分析（微信）：基本上和支付宝一样，不过支付时只需要提供6位支付密码，如果想要得到微信帐号密码的话，还需要构造一个假的登陆界面。当然了，你可能会说有短信验证，但是如果整个登录界面都是伪造的话，用户也会乖乖的帮你输入短信验证码的。并且，在iOS 8.1之前，iOS的短信也存在监控漏洞，具体请参考我们ASIACCS的论文。

 

好吧，讲到这里肯定又有人说：你的漏洞是挺严重的，但还是得往我机器上装app啊。我从来不用什么pp助手，同步推之类的，也不装什么企业应用，平时只在AppStore上下载，因为有苹果的审核，所以我肯定不会中招的。呵呵，苹果的审核真的信得过么？请看第二个demo: Google Chrome URL Scheme劫持

 

Youtube（需翻墙）: [https://www.youtube.com/watch?v=uqZPelheVdM](https://www.youtube.com/watch?v=uqZPelheVdM)

腾讯视频: [http://v.qq.com/page/x/d/4/x0149k4red4.html](http://v.qq.com/page/x/d/4/x0149k4red4.html)

 ![](http://i.imgur.com/bcn4wSQ.png)

图三、Google Chrome URLScheme劫持

 

Google公司不比阿里差吧？GoogleChrome可以算是Google在iOS上最重要的应用之一了吧？可惜的是，在该demo中Google公司的Chrome应用已经非常不幸的被App Store下载的app劫持掉了。如图三所示：演示用的app会利用Google Chrome的URL Scheme去打开Google.com这个网站。在机器上只安装Chrome的情况下，app会跳转到Chrome打开Google.com。但是当我们在App Store下载完一个叫BASCOM Browser的app之后，app却会跳转到BASCOM Browser打开Google.com。经过统计，App Store上有大量的应用声明了Chrome以及FaceBook的URL scheme，并且他们的开发者并不属于Google和Facebook。这说明Apple根本就没有保护那些热门应用的URL Scheme的想法，上传的App无论声明什么样的URL Scheme，苹果都会通过审核的。所以说，不光Chrome，Facebook有危险，支付宝，微信啥的也逃不过这一劫。

 

解决方案：1、针对iOS系统。因为BundleID在App Store上是唯一的（类似Android上的package name）苹果可以限制iOS应用不能注册别的应用的Bundle ID作为URL Scheme。这样的话，使用自己的Bundle ID作为URL Scheme的接收器就会变的安全很多。

 

2、针对第三方应用。既然苹果不发布补丁保护第三方应用。第三方应用就没有办法了么？不是的，这里至少有两种方法可以检测自己应用的URL Scheme是否被Hijack：(1)、应用本身可以发送一条URL Scheme请求给自己，如果自己可以接收到的话，说明URL Scheme没有被劫持，如果不能收到的话，就说明被劫持了，这时候可以提醒用户卸载有冲突的app。代码如下：

	[[UIApplicationsharedApplication] openURL:[NSURL URLWithString:@“alipay://test“]];

 

(2)、利用MobileCoreServices服务中的applicationsAvailableForHandlingURLScheme()方法可以所有注册了该URLSchemes的应用和处理顺序，随后应用就可以检测自己，或者别人的URL Scheme是否被劫持了。代码如下：
    Class LSApplicationWorkspace_class= objc_getClass("LSApplicationWorkspace");
    
    NSObject*workspace =[LSApplicationWorkspace_class performSelector:@selector(defaultWorkspace)];
    
    NSLog(@"openURL:%@",[workspace performSelector:@selector(applicationsAvailableForHandlingURLScheme:)withObject:@"alipay"]);
    
 

 在任意app下运行这个函数，可以返回URL处理的顺序，比如运行结果是：

    2015-03-18 15:34:37.695 GetAllApp[13719:1796928] openURL: (
    
       "<LSApplicationProxy: 0x156610010>com.mzheng.GetAllApp",
    
    "<LSApplicationProxy: 0x1566101f0>com.mzheng.Alipay",
    
       "<LSApplicationProxy: 0x15660fc30>com.alipay.iphoneclient"
    
    )

说明有三个app都声名了alipay这个URL shceme，并且会处理这个请求的是"com.mzheng.GetAllApp"，而不是支付宝。

 

PS: 各大公司是不是要开始推送iOS手机安全助手了？:-)

 

2015/03/20文章更新：刚说完支付宝，微信逃不过这一劫，我们已经在App Store上发现了劫持了支付宝的真实案例。来看demo:

 

战旗TV劫持支付宝URLScheme

Youtube（需翻墙）: [https://www.youtube.com/watch?v=O4U3l1ffUiw](https://www.youtube.com/watch?v=O4U3l1ffUiw)

Youku：[http://v.youku.com/v_show/id_XOTE3NTc0MjM2.html](http://v.youku.com/v_show/id_XOTE3NTc0MjM2.html)

 

当用户想要使用支付宝支付的时候，却跳转到了战旗TV。这里想问一下战旗TV，你到底想要干些什么呢？:-)

 

参考文献：


1．  iOS Masque Attack：https://www.fireeye.com/blog/threat-research/2015/02/ios_masque_attackre.html

 

2．  Min Zheng, Hui Xue, Yulong Zhang, Tao Wei, John C.S. Lui"Enpublic Apps: Security Threats Using iOS Enterprise and DeveloperCertificates", ACM Symposium on Information, Computer and CommunicationsSecurity (ASIACCS'15), Singapore, April 2015

 

3．  在非越狱的iPhone 6(iOS 8.1.3) 上进行钓鱼攻击 (盗取App Store密码)：http://blog.csdn.net/zhengminwudi/article/details/43916791

[原文](http://drops.wooyun.org/papers/5309)