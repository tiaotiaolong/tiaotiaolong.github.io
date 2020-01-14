---
layout:     post
title:      piwigo in_array修复方案
subtitle:   
date:       2020-01-14
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - web漏洞
    - 代码审计
            
---



先知上有一篇文章分析了piwigo2.7.1 sql注入的问题。利用in_array的弱比较问题。这样就能不返回false，从而执行下面的sql语句了。

![](http://tiaotiaolong2.cn-bj.ufileos.com/blog29-01.jpg)


很明显他这里并没有采用使用in_array()的强比较方法，而是使用正则来匹配传入的rate，正则要求必须全为数字才可以。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog29-02.jpg)



