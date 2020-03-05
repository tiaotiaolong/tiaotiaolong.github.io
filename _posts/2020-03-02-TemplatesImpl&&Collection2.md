---
layout:     post
title:      TemplatesImpl&&Collection2
subtitle:   
date:       2020-03-02
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - java安全
            
---


#### TemplatesImpl

TemplatesImpl这个调用链之前在Fastjson中就出现过了。除此之外，在Fastjson中也有利用JNDI注入做调用链的。今天主要是想记录一下CommonsCollection2调用链，发现里面也同样包含了TemplatesImpl的利用过程，先把TemplatesImpl调用链的利用过程画出来。实际上我们在Fastjson中是使用了他自己的默认调用属性的get，set方法导致调用起getOutputProperties()函数的。其中涉及的私有属性在反序列化的过程中需要得到Fastjson的支持才可以。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog31-01.jpg)

#### CommonsCollection2

在CommonsCollection2中和CommonsCollection1的调用链就完全不一样了，而且依赖的版本也变成了4.0了。但是这个调用链我之所以分开写成2张图，是因为我有个疑问，就是在CL2中调用到InvokerTransformer的时候完全可以命令执行了呀，为什么还需要间接使用了一下TemplatesImpl。难道是TemplatesImpl是依赖于字节码，命令执行的攻击面更广吗，这块我暂时还没有调试成功，看来自己构造POC的能力还是欠缺的很啊，这块我接下来会慢慢调试一下，如果有看官看懂了，不防邮件指点我一番。

![](http://tiaotiaolong2.cn-bj.ufileos.com/blog31-02.jpg)










