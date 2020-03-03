---
layout:     post
title:      记录一下jndi注入JdbcRowSetImpl
subtitle:   
date:       2020-03-03
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - java安全
            
---


#### JdbcRowSetImpl

在Fastjson里最开始是用这个JdbcRowSetImpl这个类做JNDI注入的。攻击者搭建一个自己的恶意RMI服务。通过返回的Reference对象来指定location，达到加载恶意类的目的。

这个调用过程可以算上比较简单，我在idea搜索这个JdbcRowSetImpl这个类的时候没搜到，暂时还没想通。就用了报异常的方式，让idea主动打印出来。

![](http://tiaotiaolong2.cn-bj.ufileos.com/blog32-01.jpg)


进入到JdbcRowSetImpl这个类里会发现connect函数就是调用jndi接口的过程，lookup函数调用了自己的datasource属性。

![](http://tiaotiaolong2.cn-bj.ufileos.com/blog32-02.jpg)


fastjson调用了setAutocommit函数，这个函数中调用了connect
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog32-03.jpg)


整体的调用链比较清晰，和URLDNS差不多。对我这种初学者比较友好。




