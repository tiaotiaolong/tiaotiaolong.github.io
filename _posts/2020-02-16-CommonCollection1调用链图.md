---
layout:     post
title:      CommonCollection1调用链图。
subtitle:   
date:       2020-02-16
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - java安全
            
---

## CommonCollection1

在CommonCollection1中，这个调用链应该是最为被人讨论的一个，相比URLDNS调用链，这个可以说比较复杂了，我个人在学习的过程中，发现最难理解的就是java动态代理导致的从readObject函数里的setEntry()调用到invoke()。

所以这里我画了一个调用链，方便恢复思路。其实在ysoserial中，隐藏的反序列化的思路非常值得探索。

![](http://tiaotiaolong2.cn-bj.ufileos.com/blog30-01.jpg)



