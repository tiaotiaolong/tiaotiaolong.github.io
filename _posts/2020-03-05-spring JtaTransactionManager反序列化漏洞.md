---
layout:     post
title:      spring JtaTransactionManager反序列化漏洞
subtitle:   
date:       2020-03-05
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - java安全
            
---


#### JtaTransactionManager

在spring家族中，其中安全问题比较多的是SpEL表达式注入问题了。所以基本Spring的安全问题基本都会造成RCE。今天来分析一下Spring的一个反序列化问题，是一个标准的JNDI注入安全问题，也可以理解成是一个调用链。看你在什么场景下使用它罢了。国外的安全研究员在github上推了一个demo(https://github.com/zerothoughts/spring-jndi)，不妨拉下来本地调试分析一下这个安全问题。

这个项目我是用2个idea进程分别打开里面的server和client。两个项目都是利用maven构建的，他会自动的帮我们把依赖下到.m2目录下。首先给server指定端口开启一个服务，这个服务主要做反序列化操作。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog33-01.jpg)
client端的代码同样也很简单，首先是启动一个http服务，熟悉rmi和jndi的朋友们知道是为了让服务器远程加载我们的class文件，利用反射等jvm机制加载并初始化这个类。然后开启rmi服务，生成一个reference，这个reference设置的类是恶意类，并且指定了factory location为本机地址。这样当服务器准备加载这个类的时候，自己的classpath路径下没有找到的情况，就会主动远程加载恶意类。第三部就是构造好序列化的数据，发送给服务器。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog33-02.jpg)

这么说比较乱，一图胜千言。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog33-03.jpg)

关于JtaTransactionManager的反序列化过程也很清晰，问题就出在readobject函数中，调用栈非常浅。

![](http://tiaotiaolong2.cn-bj.ufileos.com/blog33-04.jpg)

![](http://tiaotiaolong2.cn-bj.ufileos.com/blog33-05.jpg)

![](http://tiaotiaolong2.cn-bj.ufileos.com/blog33-06.jpg)

最终执行到我们的恶意类
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog33-07.jpg)













