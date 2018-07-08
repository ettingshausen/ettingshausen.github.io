---
layout: cayman
title:  "VirtualBox 调整VMDK大小"
date:   2018-07-06 14:44:36 +0800
categories: Tutorial
published: true
author: ettingshausen
--- 

Windows 10 有些吃硬盘，VirtualBox 创建的虚拟机给了50G的空间，没几天就快满了。  
`VBoxManage` 提供了 `modifymedium` 方法可以调整虚拟硬盘的大小，但是支持 `VDI` 和 `VHD` 两种格式。VBoxManage 默认创建的 `VMDK`，只能通过转换，然后才能调整。方法如下：

```shell
# Clone the .vmdk image to a .vdi.
vboxmanage clonehd "virtualdisk.vmdk" "new-virtualdisk.vdi" --format vdi
# Resize the new .vdi image (30720 == 30 GB).
vboxmanage modifyhd "new-virtualdisk.vdi" --resize 30720
# Optional; switch back to a .vmdk.
VBoxManage clonehd "cloned.vdi" "resized.vmdk" --format vmdk
```

>**注意：** 执行命令前需要先将虚拟机关机。  

转换完毕之后，再虚拟机的设置中替换掉虚拟硬盘。启动后，需要在磁盘管理中扩展分区，否则新增加的空间，不是直接可用的，在磁盘管理中可以看到这部分空间是“未分配”状态。  
![](http://wx4.sinaimg.cn/large/685ea4faly1ft06wfeofhj20lc0fvjsd.jpg)  

或者使用 [`Diskpart`](https://docs.microsoft.com/en-us/windows-server/storage/disk-management/extend-a-basic-volume) 工具扩展分区。