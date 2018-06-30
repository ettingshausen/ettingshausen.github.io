---
layout: cayman
title:  "VirtualBox 使用命令行启动虚拟机"
date:   2018-06-30 21:25:15 +0800
categories: Tutorial
published: true
author: ettingshausen
--- 

最近公司得服务器总是因为各种原因被重启，重启了之后，跑在VirtualBox虚拟机中得应用就都挂掉了，必须手工启动，甚是麻烦。好在VirtualBox提供了 `VBoxManage` 的一个命令行工具。

```sh
# start Win10 in VirtualBox
VBoxManage startvm "Win10" --type headless
```

`startvm` 支持4个参数，分别是 `gui`, `headless`, `sdl`, `separate`， 其中，`gui` 是缺省值，启动时会同时启动GUI 窗口。 `headless` 则与之相反，启动时不会启动GUI 窗口。详细命令参考[官方文档](https://www.virtualbox.org/manual/ch08.html#vboxmanage-startvm)。

将命令行保存成批处理文件，然后添加一个任务计划，当系统启动时执行脚本就可以了。

只要虚拟机启动了，就可以使用 `远程桌面连接` 远程到虚拟机， Linux系统可以使用SSH连接，操作体验和真机并无差异。

