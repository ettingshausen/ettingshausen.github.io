---
layout: post
title:  "魅族 MEIZU flyme 免root禁用系统应用"
date:   2016-07-07 16:05:06 +0800
categories: Android
---


前段时间买了个魅蓝note3，2G的运存略微显得有些紧张。有些系统应用用不到，其实不用root权限也可以禁用掉。

操作较为简单，简要步骤如下：
#### 1.安装adb驱动
adb驱动一般手机连接到电脑，自动就装好了。如果手机较为冷门又嫌麻烦，可以现在电脑上安装个豌豆荚，手机连接电脑，驱动会自动下载安装。
#### 2.在手机上打开USB调试
>`设置->  辅助功能->开发者选项-> USB调试`

#### 3.在命令提示窗口输入命令
执行命令之前，先要有adb的环境，其实就是Android SDK中的platform-tools，这里注意一下，新版的flyme系统基于Android5.1+，老版本的platform-tools可能会有问题

![platform-tools](http://upload-images.jianshu.io/upload_images/1335634-87dd0700022e0ae1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```shell
adb shell pm hide com.meizu.flyme.gamecenter
adb shell pm hide com.meizu.net.o2oservice
adb shell pm hide com.meizu.Flyme.gamecenter
adb shell pm hide com.meizu.media.life
adb shell pm hide com.meizu.media.reader
adb shell pm hide com.meizu.media.ebook
adb shell pm hide com.meizu.compaign
adb shell pm hide com.meizu.gamacenter.service
adb shell pm hide com.meizu.net.search
adb shell pm hide com.meizu.voiceassistant
adb shell pm hide com.meizu.flyme.easylauncher
adb shell pm hide com.meizu.net.map
adb shell pm hide com.meizu.customizecenter
adb shell pm hide com.meizu.media.music
adb shell pm hide com.meizu.flyme.service.find
adb shell pm hide com.meizu.gamecenter.service
adb shell pm hide com.meizu.flyme.wallet
adb shell pm hide com.meizu.netcontactservice
adb shell pm hide com.android.browsers
adb shell pm hide com.meizu.yellowpage
adb shell pm hide com.meizu.flyme.childrenlauncher
adb shell pm hide com.meizu.flyme.telecom
adb shell pm hide com.meizu.feedback
```

---
解除
```shell
adb shell pm unhide com.meizu.flyme.gamecenter
adb shell pm unhide com.meizu.net.o2oservice
adb shell pm unhide com.meizu.Flyme.gamecenter
adb shell pm unhide com.meizu.media.life
adb shell pm unhide com.meizu.media.reader
adb shell pm unhide com.meizu.media.ebook
adb shell pm unhide com.meizu.compaign
adb shell pm unhide com.meizu.gamacenter.service
adb shell pm unhide com.meizu.net.search
adb shell pm unhide com.meizu.voiceassistant
adb shell pm unhide com.meizu.flyme.easylauncher
adb shell pm unhide com.meizu.net.map
adb shell pm unhide com.meizu.customizecenter
adb shell pm unhide com.meizu.media.music
adb shell pm unhide com.meizu.flyme.service.find
adb shell pm unhide com.meizu.gamecenter.service
adb shell pm unhide com.meizu.flyme.wallet
adb shell pm unhide com.meizu.netcontactservice
adb shell pm unhide com.android.browsers
adb shell pm unhide com.meizu.yellowpage
adb shell pm unhide com.meizu.flyme.childrenlauncher
adb shell pm unhide com.meizu.flyme.telecom
adb shell pm unhide com.meizu.feedback
```
语法详见[ADB Shell Commands](https://developer.android.com/studio/command-line/shell.html)

