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
+ Part 2 – Variables
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
假设你使用的是Windows XP 或者更新的版本，我建议保存的批处理文件，文件扩展名为 `.cmd`。 一些过时的Windows版本使用 `.bat`，因此我推荐更为现代的  `.cmd` 来避免 [`.bat` 带来的副作用](http://waynes-world-it.blogspot.fr/2008/08/difference-between-bat-and-cmd.html)。  

使用.cmd文件扩展名，你可以使用你喜欢的文件名。建议文件名中避免使用空格，空格在脚本中令人头疼。有个简单办法，可以避免使用空格，使用 Pascal 命名法则来命名(例如： `HelloWorld.cmd` 代替 `Hello World.cmd`)。也可以使用标点符号，比如 `.` 或者 `-` (例如： `Hello.World.cmd`, `Hello-world.cmd`, `Hello_World.cmd`)。  

批处理文件命名另外一个需要注意的是，命名避免与系统内置的命令或者一些常用的软件相同。例如，避免使用 `ping.cmd`，因为系统中有个 `ping.exe`的可执行文件。如果运行`ping`命令，你真正想要运行的是 `ping.cmd`, 但可能无意调用的是 `ping.exe`， 这样就显得很混乱。我可能会将脚本命名为 `RemoteHeartbeat.cmd` 或者类似的东西为脚本的名称添加一些上下文，避免与其他可执行文件命名冲突。当然，在特殊情况下，不得不修改 `ping` 的默认行为，那这个命名的建议就无所谓了。