---
layout: post
title:  "使用Charles抓包原理"
date:   2016-05-01 11:36:23
---
要使用Charles抓包，需要手机电脑连接同一WiFi，手动代理连接服务器（电脑连接的IP地址），端口号为8888，这时候使用手机上网的过程中就会经过电脑，此时连接的电脑就相当于一个中间人，任何手机上的数据都通过中间人二次转发，而在Charles中可以看到拦截到的数据的接口，使用青花瓷切换结构页面后可以轻松更换返回的接口数据进行断点调试。所以在公共场所连接的WIFI需要小心使用，一些擅长路由器开发拦截的黑客可以轻松获取或者篡改手机应用程序。
