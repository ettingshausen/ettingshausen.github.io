---
layout: post
title:  "Windows10 tensorboard"
date:   2017-06-02 10:23:06 +0800
categories: Tensorflow
---

网上关于`tensorboard`的方法非常的多，但是大多数都是针对Linux或者Mac系统。

Windows现在仅支持Python3.5.x版本，如果使用其他的Python版本可能会提示找不到包。

安装好Python之后，使用pip命令安装

```bash
pip install tensorflow===1.1.0
```

安装完成之后就可以直接使用`tensorboard`命令了。

但是，Windows系统的路径包含":",比如`C:\tmp\tensorflow\mnist\logs`，然而在`tensorflow  `中`:`是个分隔符，所以导致路径不能被正确解析。

解决办法如下：

```bash
tensorboard --logdir=training:C:\tmp\tensorflow\mnist\logs
```

参考[issuse7856](https://github.com/tensorflow/tensorflow/issues/7856)

然后就可以在浏览器中打开http://127.0.0.1:6006/


![image.png](http://upload-images.jianshu.io/upload_images/1335634-3c764eb99d8232a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)