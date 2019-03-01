---
layout:     post
title:      利用空白符绕过 palindrome ctf解析
subtitle:   
date:       2019-02-12
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - php
    - ctf
    
---


在php语言中有很多的函数特性，本意是增加语言的灵活性，但是这些函数特性有时候组合到一起会引起一些和我们理所应当的东西有些违背，导致了安全问题。

这道题的源码：

![](http://tiaotiaolong.cn-bj.ufileos.com/blog011-0.jpg)
想要输出flag就必须将\$flag变量赋值到\$info上,然后输出出来。
首先是要绕过is_numeric() 这里不能是数字。我们来看一下numeric的源码
![](http://tiaotiaolong.cn-bj.ufileos.com/blog011-1.jpg)
这里对空白符进行跳过，目的是提高效率，**这里没有NUL**。
再来看看trim函数
![](http://tiaotiaolong.cn-bj.ufileos.com/blog011-2.jpg)
这里对空格 回车 换行 制表符 垂直制表符 以及NUL进行trim 也就是会删除掉这6个。**但是这里没有\f**

所以当我构造**number为%00%0c121**的时候 其中%00是is_numeric中没有的，这里就绕过了数字判断。
![](http://tiaotiaolong.cn-bj.ufileos.com/blog011-3.jpg)

然后进入了算法校验中，我们的目的是为让让算法返回false 这里的\f就起到了作用。
![](http://tiaotiaolong.cn-bj.ufileos.com/blog011-4.jpg)
这里比较第一位和最后一位是否相等 直接返回false。由于intval中同样对\f进行了跳过，所以这里value1和value2的校验是不受影响的。
![](http://tiaotiaolong.cn-bj.ufileos.com/blog011-5.jpg)





