---
layout: cayman
title:  "Windows 批处理脚本指南: 开始"
date:   2017-12-10 00:39:48 +0800
categories: cmd
published: true
excluded: true
author: ettingshausen
---  

>
+ [Overview]({% post_url 2017-12-09-guide-to-windows-batch-scripting %})
+ Part 1 – 开始
+ [Part 2 – 变量]({% post_url 2017-12-12-windows-batch-variables %})
+ Part 3 – Return Codes
+ Part 4 – stdin, stdout, stderr
+ Part 5 – If/Then Conditionals
+ Part 6 – Loops
+ Part 7 – Functions
+ Part 8 – Parsing Input
+ Part 9 – Logging
+ Part 10 – Advanced Tricks    


# Getting Started with Windows Batch Scripting
---  
Windows批处理脚本兼容性非常好 - 它适用于任何现代Windows机器。 你可以在任何一台现代Windows机器上创建和修改批处理脚本。 这些工具从盒子里出来：Windows 命令提示符和一个像 `Notepad.exe` 这样的文本编辑器。 这绝对不是最好的shell脚本语言，但它能完成工作。   

# Launching the Command Prompt
---
 使用快捷键 `Windows Logo Key` + `R`， 输入 `cmd.exe` 然后按回车键，运行 Windows 命令提示符。这比从开始菜单找到命令提示符要快的多。  


# Editing Batch Files  
 --- 

 批处理文件的通用文本编辑器是记事本(`Windows Logo Key` + `R`， 输入 `notepad.exe`, 回车)。由于批处理文件只是ASCII文本，所以可以使用任何文本编辑器或文字处理软件。很少有编辑器能做到语法高亮，或者关键字的支持，所以记事本已经足够好了，它预装在每一台Windows系统上。


# Viewing Batch Files  
---
我坚持使用记事本查看批处理文件。在Windows资源管理器（又名“我的电脑”）中，你可以记事本中查看批处理文件，方法是右键单击该文件并从文菜单中选择“编辑”。如果你想在命令提示符窗口中查看文件内容，可以使用DOS命令，例如 `TYPE myscript.cmd` 或者 `MORE myscript.cmd`或者 `EDIT myscript.md`。(在 Windows 10 中，`EDIT` 不可用)

# Batch File Names and File Extensions  
---
假设你使用的是Windows XP 或者更新的版本，我建议保存的批处理文件，文件扩展名为 `.cmd`。 一些过时的Windows版本使用 `.bat`，因此我推荐更为现代的  `.cmd` 来避免 `.bat` [带来的副作用](http://waynes-world-it.blogspot.fr/2008/08/difference-between-bat-and-cmd.html)。  

使用.cmd文件扩展名，你可以使用你喜欢的文件名。建议文件名中避免使用空格，空格在脚本中令人头疼。有个简单办法，可以避免使用空格，使用 Pascal 命名法则来命名(例如： `HelloWorld.cmd` 代替 `Hello World.cmd`)。也可以使用标点符号，比如 `.` 或者 `-` (例如： `Hello.World.cmd`, `Hello-world.cmd`, `Hello_World.cmd`)。  

批处理文件命名另外一个需要注意的是，命名避免与系统内置的命令或者一些常用的软件相同。例如，避免使用 `ping.cmd`，因为系统中有个 `ping.exe`的可执行文件。如果运行`ping`命令，你真正想要运行的是 `ping.cmd`, 但可能无意调用的是 `ping.exe`， 这样就显得很混乱。我可能会将脚本命名为 `RemoteHeartbeat.cmd` 或者类似的东西为脚本的名称添加一些上下文，避免与其他可执行文件命名冲突。当然，在特殊情况下，不得不修改 `ping` 的默认行为，那这个命名的建议就无所谓了。    

# Saving Batch Files in Windows
---
默认情况下，记事本会尝试将所有文件都保存为纯文本文件。要使记事本保存扩展名为 `.cmd` 的文件，需要将“保存类型”更改为“所有文件(.)”  如下图所示。

![image.png](http://upload-images.jianshu.io/upload_images/1335634-ffdfc67b53d4ca1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
*保存文件名为 `hellowrold.cmd`*  
{:.image-caption}   
  
>**补充**：在截图中使用了一个快捷方式，后续好介绍更多。通过命名 `%USERPROFILE%\HelloWorld.cmd` 把文件保存到 `用户资料` 文件下。`%USERPROFILE%` 是Windows系统的一个环境变量，它指向你的 `用户`目录，如：`C:\Users`。在较新的Windows系统上，`用户资料` 指的是 `C:\Users\<用户名>`。这能够节约一点时间，因为命令提示符默认的工作目录就是 `C:\Users\<用户名>`。这样运行`HelloWorld.cmd` 脚本就不需要切换目录。  

# Running your Batch File
---
在Windows中运行批处理文件的简单方法是双击Windows资源管理器（又名“我的电脑”）中的批处理文件。然而这样操作，命令提示符不会给你多少时间看到输出或者任何错误，脚本运行完成后就立即退出，你只能看到一个窗口一闪而过。（我们将在Part 10 – Advanced Tricks 部分中学习如何处理这个问题）。  

运行新编写的脚本，我们需要在现有的命令窗口中运行它。对于新手来说，最简单的方法就是把脚本拖放在命令提示窗口中。命令提示符将在命令行中输入脚本的完整路径，并包含空格路径用双引号括起来（如果不包含空格，则不会），如下图所示。  

![image.png](http://upload-images.jianshu.io/upload_images/1335634-a2ba61c718bab6c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
*将包含空格的路径用引号括起来*  
{:.image-caption}     


其他的一些技巧：

+ 通过`向上键`和`向下键`来浏览之前运行过的命令。
+ 像这样来运行脚本：`%COMPSPEC% /C /D "C:\Users\User\SomeScriptPath.cmd" Arg1 Arg2 Arg3` 这样会启动一个新的命令提示符，并在这个进程中运行脚本。 `/C` 的意思是在脚本运行完成后退出子进程。`/D` 是禁止所有自动运行的脚本。这样做的原因是可以阻止命令提示窗口自动关闭，如果我写的脚本或者调用的脚本中调用了 `EXIT` 命令。`EXIT` 命令会自动关闭命令提示符窗口，除非是从子命令提示符进程中调用 `EXIT`。

# Comments
---
官方的注释方式是使用 `REM` (Remark) 关键字。 
```bash
REM This is a comment!
```  
还有一种经常被忽略掉的高级用法，使用 `::`，两个冒号。 

```bash
:: This is also a comment too! (usually!!)
``` 

多数的高级作者发现，`::` 相比 `REM` 能减少注意力分散，但是注意，有几个地方 `::` 会导致错误。  

比如，一个 for 循环中使用 `::` 就会导致错误。如果有这种情况，只需要换回 `REM` 即可。  

# Silencing Display of Commands in Batch Files
---
批处理文件中的第一个非注释行，通常是一条关闭输出的命令。

```bash
@ECHO OFF
```  

`@`  是一个特殊的操作符，用于抑制命令行的打印。一旦我们关闭了输出，在后续的脚本命令中就不再需要 `@` 操作符了。 
可以使用以下命令恢复打印:
```bash
ECHO ON
```    
当脚本退出时，命令提示符会自动将ECHO恢复到它以前的状态。  

# Debugging Your Scripts
---
批处理文件涉及大量的试验和错误编码。我不知道有什么真正的Windows批处理脚本调试器。更糟的是，我也不知道如何将命令处理程序的输出设置成详细状态，以帮助解决脚本问题（这是Unix/Linux脚本的常用技术。）。使用 `ECHO` 命令打印自定义的临时(ad-hoc[^ad-hoc])调试消息是唯一的选择。高级脚本编写者可以通过一些技巧来有选择地打印调试消息，我更喜欢在脚本运行正常时删除调试/检测代码。


# 参考资料
---


[^ad-hoc]: [Ad hoc](https://zh.wikipedia.org/wiki/Ad_hoc)是拉丁文常用短语中的一个短语。这个短语的意思是“特设的、特定目的的（地）、即席的、临时的、将就的、专案的”。