---
layout: post
title:  "iOS开发静态库"
date:   2016-04-07 12:36:23
---
.a文件

1、新建项目，点击iOS—Framework&—Cocoa Touch Static Library。

给你的工程命名为libnam

2、系统自动生成以工程名命名的.h和.m文件，可自定义的在目录下添加或删除文件，注意目录下Products文件夹有一个.a文件为红色，说明文件并不存在。这里我们将系统生成的.h和.m文件删除。

将所有文件导入工程目录下

3、点击Build Phases—Copy Files，左下角点击+号按钮，添加你需要暴露的接口头文件。如果你在静态库工程中使用了category，那么你可能会碰到链接问题，解决的办法就是需要同时在生成静态库的工程和使用静态库的工程中使用“-all_load”编译选项，即在对应target的"Build Settings"中的“Other Linker Flags”选项添加“-all_load”

点击Add添加

4、然后点击左上角，选择Edit Scheme，Build Configuration下选择Release，先注意检查下面Release是否为NO：Yes表示只编译选中模拟器设备对应的架构，No则为编译所有模拟器设备支持的cup架构（Debug版本同理）,选择NO，然后分别在模拟器和真机下Command+B编译一下，会看到Products文件夹下的.a文件变为黑色，这个.a文件就是我们想要得到的静态库。



注：关于静态库对CPU架构的支持，首先了解iOS设备CPU架构方面的知识，ARM是微处理器行业的一家知名企业，arm处理器以体积小和高性能的优势在嵌入式设备中广泛使用，几乎所有手机都是使用它的。

模拟器：iphone4s~5 : i386 iphone5s~6plus : x86_64

真机：iphone3gs~4s : armv7  iphone5~5c : armv7s （静态库只要支持了armv7，就可以跑在armv7s的架构上） iphone5s~6plus : arm64

armv6, armv7, armv7s是ARM CPU的不同指令集，原则是向下兼容的。例如iPhone4S CPU支持armv7, 但它同时兼容armv6，只是使用armv6指令可能无法充分发挥它的特性。

这里再补充一下查看静态库.a对处理器架构的支持，先cd到.a文件的路径下，命令行输入：lipo -info libnam.a

5、通过终端打开路径/Users/libnam/Library/Developer/Xcode/DerivedData/，选择对应的工程文件夹。

打开Build—Products文件夹，会看到Release-iphoneos和Release-iphonesimulator文件夹，分别是真机和模拟器的.a文件，为了使用方便我们将两个版本的.a文件合并。

6、合并真机和模拟器.a文件，在终端输入以下命令行：lipo -create  模拟器.a文件的路径 真机.a文件的路径 -output 合并后的保存路径（例：lipo -create /Users/libnam/Library/Developer/Xcode/DerivedData/StaticLib/Build/Products/Release-iphoneos/libxxx.a  /Users/libnam/Library/Developer/Xcode/DerivedData/StaticLib/Build/Products/Release-iphonesimulator/libxxx.a  -output /Users/libnam/Desktop/libnam.a）最终会在桌面得到一个合并后的libnam.a文件，再将暴露出来的.h头文件一起复制出来。

7、使用：只需将.a和暴露出来的.h头文件导入工程目录下就可供外界使用。