---
layout:     post
title:      gunicorn搭配vtest的一个小坑
subtitle:   
date:       2019-11-01
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 环境搭建
            
---

(文章版权归笔者跳跳龙所有，谢绝转载)


好久没有更新博客了，最近在密谋一件大事情，可能需要的时间很久很久，也可能很短很短。就好像我曾经在上大学的时候，在qq空间里发了一条说说，说我结束送水生涯，因为当时我在心里就埋下了学习安全的种子，于是那时便把所有的精力都融入到了喜欢的事情上！原本如此平凡的一天，从现在来看，相当的有意义。

最近在研究一些东西，想用vtest做无回显漏洞验证。但是这个vtest要想上线，需要和gunicorn一起协同工作才能保持稳定性。

这里要把vtest的代码移出去，移到这个main函数之外就可以
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog27-01.jpg)

这么做的原因是gunicorn调用的是app，最开始我以为是调的主函数。所以这里的代码结构需要改一下。所以这样当gunicorn调用app的时候 DB就已经被初始化了！


