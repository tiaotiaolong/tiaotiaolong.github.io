---
layout:     post
title:      Android 动态调试so dvmDexFileOpenPartial函数dump脱壳
subtitle:   
date:       2019-06-10
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - android
    - 逆向
        
---


在android动态调试smali中的结尾我已经预告要出一篇so动态调试的博客了。今天正好时间充裕，顺手也复习一下之前的环境和琐碎的知识点，android的知识点太碎了！！！

在很多android一代壳中，大多数的脱壳方案都是基于dvmDexFileOpenPartial处hook实现的。由于一代壳的主要加固方式是隐藏真正的dex，故，找准时机dump就成为了较为通用的方法。这个函数有2个重要参数，分别是dex文件的指针和文件长度大小。那么在调用dvmDexFileOpenPartial函数之前，在内存中找到这2个值，便找到了真正的dex文件。

这里主要记录一下步骤：(android上的东西调试起来真的很麻烦)
1.首先在android上执行 android_server (这个文件在ida的安装目录下，把这个文件push到/data/local/tmp目录下即可)，这个时候android会监听23946端口

![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-01.jpg)

2.接下来进行端口转发，实现2台机器的端口互联。

```
adb forward tcp:23946 tcp:23946
```

![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-02.jpg)

3.调试启动app，这样启动和正常点击app启动的区别在于可以启动之后再很早的地方断住(包名在在mainfest文件中会有)
![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-04.jpg)



```
adb shell am start -D -n "你的程序的Activity全路径"
```
![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-07.jpg)

这个时候手机会出现等到调试的字样。

4.打开IDA attach目标进程 可以利用搜索包名来寻找
打开IDA设置，勾选debugger option
![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-03.jpg)




5.jdb attach上调试器



```
jdb -connect com.sun.jdi.SocketAttach:hostname=127.0.0.1,port=8700

```
![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-05.jpg)



如果connect不上，先执行adb forward tcp:8700 jdwp:(被调试进程的pid)在执行上述jdb命令
![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-06.jpg)



6 分析基地址和偏移地址在dvmDexFileOpenPartial函数下断点

libdvm.so基地址：
![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-08.jpg)
偏移地址：
![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-09.jpg)

经过相加计算，下断点

![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-10.jpg)

7.启动IDA 运行到断点出 看见R0 R1 R0代表dex文件指针 R1代表文件大小
![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-11.jpg)

R0数据窗口 已经找到了dex文件
![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-12.jpg)

接下来就是把这个dex文件dump下来就可以了
Shift+F2 
![](http://tiaotiaolong.cn-bj.ufileos.com/blog17-13.jpg)

代码就是写文件的操作，很简单，剩下的就看大家自己分析了。后面继续更新一些android脱壳相关的东西。



