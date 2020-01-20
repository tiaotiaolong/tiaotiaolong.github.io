---
layout:     post
title:      shiro反序列化漏洞以及URLDNS
subtitle:   
date:       2020-01-10
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - java安全
            
---


这个漏洞在我司还挺常见的，最开始是源于林鹏给了一个网站，说这个洞有shiro反序列化漏洞，但是一直没有研究，后来写扫描器插件的时候，用扫描器扫了一下公司的网站，扫出来3个shiro反序列化的漏洞。我去，可怕。

我这里是用github的shiro代码，拉下来自己,自己打包的，打好的war包我会上传至github。

github地址：https://github.com/tiaotiaolong/tiaoVulenv/tree/master/shiro_deser


seebug上这里面的环境是自己在pom里添加的collention4，因为数组的bug，正常来说如果没有这个调用链是没法起到复现效果的。

![](http://tiaotiaolong2.cn-bj.ufileos.com/blog28-01.jpg)

大家可以用我的war包试着复现一下，另外平时用扫描器扫的时候都是使用DNSLOG实现的，用的都是基础类。

DNSLOG用的就是ysoserial中的URLDNS调用链，这个调用链应该是ysoserial中最简单的调用链，而且不需要第三方库的支持。

关于URLDNS大家可以在本地模拟一个读取序列化数据的代码，我这里简单写一个：
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog28-02.jpg)

然后我们读取ysoserial生成的URLDNS shellcode
会发现反序列化之后是一个hashmap对象，在readobject的时候，变会断在hashmap的readobject函数。
最终函数即将执行完毕的时候，会在putval中进行处理，处理之前会调用hash函数。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog28-03.jpg)
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog28-04.jpg)

hash函数自然而然会调用hashcode函数，在hashcode函数中，就会获取主机地址了。整个调用过程十分简单

![](http://tiaotiaolong2.cn-bj.ufileos.com/blog28-05.jpg)

Hashmap.readobject
putval
hash
hashcode
getHostAddress

恩恩 URLDNS应该比Collection简单多了。




seebug原文： https://paper.seebug.org/shiro-rememberme-1-2-4/


