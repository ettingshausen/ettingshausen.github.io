---
layout: cayman
title:  "制作 ThinkPad BIOS USB 升级镜像"
date:   2019-12-17 16:56 +0800
categories: note
published: true
author: ettingshausen
---    

![ThinkPad-E450c-BIOS](https://user-images.githubusercontent.com/9806325/70990523-b0e90800-2100-11ea-97c6-fa8b281f595c.png)  

联想在官网提供了 BIOS 的历史版本，有两个版本，一种是 `exe` 可以直接在 Windows 平台下执行升级（PE 环境也可以）， 还有一种是 `iso` 需要烧录在 CD/DVD 通过光驱来运行。现在光驱已经逐渐退出历史舞台了，现在的市场上的电脑，很难再能找到光驱这个外设了。 还有一种办法，就是通过 U 盘制作成可启动的系统盘，虽然官网的备注给的是 `Bootable CD` 但是并不能直接制作成启动盘，这是因为这个镜像是 El Torito 规格需要经过修改才可以。

![](https://user-images.githubusercontent.com/9806325/70991241-0a9e0200-2102-11ea-971e-90e82607a513.png)  

### 安装 geteltorito 读取启动信息  

geteltortio 是一个 `perl` 脚本，用于解压 El Torito 格式的 `.iso`文件。

```
wget https://userpages.uni-koblenz.de/~krienke/ftp/noarch/geteltorito/geteltorito/geteltorito
chmod +x geteltorito
```  

读取 iso 到 img 文件，读取成功，在 macOS 系统可以直接双击挂载到 Finder。
```
geteltorito -o boot.img /path/to/image.iso
```
读取启动信息到 `boot.bin` 文件   
```
geteltorito -o boot.bin /path/to/image.iso
```  

双击`boot.img`挂载镜像文件，并创建一个目录，用于存放镜像内容：

```
mkdir image-folder
cp -R /Volumes/Mounted-image image-folder
```  

然后复制刚才读取好的 `boot.bin` 文件到 `image-folder`  

### 安装 mkisofs 重新打包   

`mkisofs` 可以通过 `Homebrew` 来安装 

```
brew install mkisofs
```

```
mkisofs -udf -no-emul-boot -relaxed-filenames -joliet-long -hide boot.bin -b boot.bin -D -o ./new-image.iso ~/image-folder
```  

这个时候重新打包好的 `new-image.iso` 就可以通过工具制作成可以启动的 USB 镜像了 （推荐使用 Rufus[^rufs]）。





# 参考资料
---   
[^rufs]:[Rufus](https://rufus.ie/)  
[Modify a bootable .iso image (macOS)](https://epir.at/2013/03/10/modify-a-bootable-.iso-image-macos/)  
[How to update Lenovo BIOS from Linux without using Windows](https://www.cyberciti.biz/faq/update-lenovo-bios-from-linux-usb-stick-pen/)  
[BIOS Update (Utility & Bootable CD) for ThinkPad E450, E450c, E550, E550c](https://support.lenovo.com/us/en/downloads/ds101972)