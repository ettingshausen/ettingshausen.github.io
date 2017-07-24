---
layout: post
title:  "Powershell批量重命名"
date:   2017-07-24 01:18:48 +0800
categories: Powershell
---

![2017年7月 拍摄于南窑头](http://upload-images.jianshu.io/upload_images/1335634-bbfd7fb0d1f3bd2d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  


*2017年7月 拍摄于南窑头*  
{:.image-caption}  



下载了些教学视频，再IIS上发布，这样就可以再手机上看了。但是，这些视频的文件名中包含`+`这个字符，URL中应该是个需要转义的字符，所以在浏览器中根本没法播放。

于是就想到了用Power shell把这些`+`一次性换成`-`。问题应该就解决了。

源文件的格式这样的：
>k1+lecture1.mp4

我们需要改成下面这样的：
>k1-lecture1.mp4


```powershell
Get-ChildItem *.mp4 | Rename-Item -NewName { $_.name -Replace '\+','-' }
```

看起来非常简单，只需要一行  : )

详细参考微软官方文档[Rename-Item](https://msdn.microsoft.com/en-us/powershell/reference/5.1/microsoft.powershell.management/rename-item)

PS 每次更新博客都要手写date，怎么通过PowerShell获取时间并且转换想要的格式呢？

```powershell
(Get-Date).ToString("yyyy-MM-dd hh:mm:ss +0800")
```

输出：
>2017-07-24 01:23:10 +0800