---
layout:     post
title:      phpcms sql注入漏洞分析
subtitle:   
date:       2020-04-05
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - php安全
            
---




#### 漏洞点：down.php
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog35-01.jpg)

这个漏洞点需要你传入a_k变量，sys_auth函数对a_k变量解密。所以这里需要传入一个加密变量。这里的思路猥琐就在这里，我们如何去获取一个加密值，需要找到另外一个点，这个点传入我们的sql语句，然后返回回来加密的字符串即可，这里找到的点是swfupload_json函数。这个函数会在cookie里进行返回。

#### exp攻击三步走第一步：
首先先给siteid传入一个值，会经过setcookie函数拿到cookie中的siteid加密串。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog35-02.jpg)
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog35-03.jpg)


#### exp攻击三步走第二步：
然后将明文payload传给swfupload_json函数，post数据中的userid_flash字段是第一步返回的siteid。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog35-04.jpg)
这个函数中，会拿到明文payload,会调用set_cookie函数，执行sys_auth加密函数，这样就会在set-cookie回包中拿到密文payload了。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog35-05.jpg)
之所以会在post数据中加上userid_flash，是因为在swfupload_json执行之前会执行构造函数。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog35-06.jpg)
如果这里cookie没有fXbDR__userid或者session数组里也没有userid。这里就会取userid_flash，这样为了保证我们的payload不会报错。在我实际测试中，这块我是不需要携带fXbDR__userid。因为我看我的cookie里默认就携带这个字段。


#### exp攻击三步走第三步：
从第二步中响应拿回set-cookie 填入a_k,就会调用文章最开始的down.php函数。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog35-07.jpg)

这个时候id前面的&符号，就会把id覆盖成 ‘ and updatexml() 报错语句。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog35-08.jpg)



#### 总结
这个phpcms这个注入是那种思路比较猥琐的。他的输入是一个加密语句，所以必须想办法实现加密过程。由于每台服务器上的key是无法确定的，所以这里就用cookie返回的方式作为输出，获取加密结果。

这个漏洞还是蛮有意思的！休息，去看欢乐喜剧人了，上期欢乐喜剧人有点神仙打架的意思啊！

