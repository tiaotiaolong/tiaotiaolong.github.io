---
layout:     post
title:      smali动态调试(Android Studio)
subtitle:   
date:       2019-03-11
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - android
    - 逆向
    
---

(文章版权归笔者跳跳龙所有，谢绝转载，请勿拾人牙慧)

关于android逆向中，无非就是静态分析和动态调试2中大方法。由于android应用可以使用java层和c语言层，所以针对这2部分情况分别就是smali调试和so调试。关于这2中调试我都会记录一下如何配置，如何搭建环境以及如何分析调试。主要是为了实现备忘。这里呢我主要是看了四哥的大黄书，这里也推荐一下吧，可以购买四哥的《Android应用安全防护和逆向分析》。

四哥这本书里关于调试是使用eclipse 我比较喜欢jetbrains公司的工具集。所以这里就是用Android Studio作为演示。

准备工作:
1.需要的apk文件，这里为了方便演示用的是一个网上别人写的demo 下载地址:[mytestapp](https://github.com/MartinHan01/android_reverse_practice/blob/master/mytestapp/mytestapp.apk) 

2.需要给Android Studio 安装smaliidea插件。 下载地址:[smaliidea](https://bitbucket.org/JesusFreke/smali/downloads/smalidea-0.05.zip)

有兴趣的可以去[项目主页](https://github.com/JesusFreke/smali)

开始：
1.把apk文件安装在手机上。

```
adb install testapp.apk
```

2.这里我们使用调试启动app的方式来启动app。这样启动之后app会等待debugger来通过端口转发来调试。

```
adb shell am start -D -n androiddemo.han.com.myapplication/.MainActivity
```

这里的包名和类名可以通过解包来查看AndroidManifest.xml来看。当然这样比较麻烦一些，可以通过如下命令来看。

```
adb shell dumpsys activity top
```
![](http://tiaotiaolong.cn-bj.ufileos.com/blog13-00.jpg)

3.然后我们这里打开AS 安装插件，插件的安装方法就是从AS的plugins中点击从磁盘开始安装，安装完毕后会提示重启AS。
![](http://tiaotiaolong.cn-bj.ufileos.com/blog13-01.jpg)
因为这个是第三方插件，所以我们会使用从磁盘安装。

4.安装完毕之后我们对之前使用apktools解包的smali文件夹进行导入项目，这里直接点击file-->open 打开对应的目录即可。

![](http://tiaotiaolong.cn-bj.ufileos.com/blog13-02.jpg)

5.编写运行配置文件。
![](http://tiaotiaolong.cn-bj.ufileos.com/blog13-03.jpg)

这里要监听本地的8700端口。
![](http://tiaotiaolong.cn-bj.ufileos.com/blog13-04.jpg)


6.打开ddms(高版本的DDMS已经被AS抛弃了，在用户界面已经找不到了。但是程序还是在你的sdk/tools目录下)
![](http://tiaotiaolong.cn-bj.ufileos.com/blog13-05.jpg)

直接运行即可。这里我们运行DDMS就不需要在做端口转发了，因为DDMS已经做好了。
不需要在执行

```
adb forward tcp:8700 wjdp:pid(app的进程)

```

6.经过我们的分析，我们这次在MainActivity$1文件中第54行下断点。点击debug run-->debug。开始调试，点击app中的button。会在这里断下。剩下的就是小伙伴的思维喽。


![](http://tiaotiaolong.cn-bj.ufileos.com/blog13-06.jpg)


后记：环境只是调试的垫脚石，思路才是王道！！！ 后面更新调试Android IDA 调试Android so库。


