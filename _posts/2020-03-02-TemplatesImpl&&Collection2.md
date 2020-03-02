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

 TemplatesImpl这个调用链之前在Fastjson中就出现过了。除此之外，在Fastjson中也有利用JNDI注入做调用链的。今天主要是想记录一下CommonsCollection2调用链，发现里面也同样包含了TemplatesImpl的利用过程，先把TemplatesImpl调用链的利用过程画出来。实际上我们在Fastjson中是使用了他自己的默认调用属性的get，set方法导致调用起getOutputProperties()函数的。其中涉及的私有属性在反序列化的过程中需要得到Fastjson的支持才可以。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog31-01.jpg)





