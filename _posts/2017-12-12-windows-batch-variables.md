---
layout: cayman
title:  "Windows 批处理脚本指南: 变量"
date:   2017-12-12 20:20:36 +0800
categories: cmd
published: true
excluded: true
author: ettingshausen
---  

>
+ [Overview]({% post_url 2017-12-09-guide-to-windows-batch-scripting %})
+ [Part 1 – 开始]({% post_url 2017-12-10-windows-batch-getting-started %})
+ Part 2 – 变量
+ Part 3 – Return Codes
+ Part 4 – stdin, stdout, stderr
+ Part 5 – If/Then Conditionals
+ Part 6 – Loops
+ Part 7 – Functions
+ Part 8 – Parsing Input
+ Part 9 – Logging
+ Part 10 – Advanced Tricks      

今天我们将介绍变量，这些变量在任何非平凡的批处理程序中都是必需的。变量的语法可能有点奇怪，所以了解变量以及它的使用方法将是有帮助的。

# Variable Declaration
---
DOS不需要声明变量。未声明/未初始化变量的值是空字符串或`""`。大多数人喜欢这样，因为它减少了需要编写的代码量。就我个人而言，我喜欢在使用变量之前声明一个变量，因为它可以捕获了一些简单的错误，比如变量名中的输入错误。  

# Variable Assignment  
---
使用 `SET` 命令为一个变量赋值。

```bash
SET foo=bar
```
>**注意**：不要在名称和值之间使用空格；`SET foo = bar` 将不起作用，但 `SET foo=bar` 就可以。  

`/A` 开关支持算数操作。这是一个有用的工具，如果您需要验证该用户输入是一个数值。 
```bash
SET /A four=2+2
4
```  
一般的惯例，变量经常使用小写名称，系统变量（环境变量）一般是大写。这些环境描述了在您的系统中哪里可以找到某些东西，例如 `%TEMP%`，它是临时文件的路径。DOS是不区分大小写的，所以这个惯例并不是强制的，但是让脚本更容易阅读或者故障排除，倒是个好主意。
>**警告**：SET终覆盖任何现有变量。在编写脚本时，先验证下是否覆盖了系统范围的变量。`ECHO %foo%`可以快速确认 `foo` 是不是一个现有的变量。例如，很容易将一个变量命名为 “temp”，但这将会改变广泛使用的 “%temp%” 环境变量的含义。DOS包含一些“动态”的环境变量，它们的行为更像是命令。这些动态变量包括 `%DATE%`、`%RANDOM%`和 `%CD%`。最好不要覆盖这些动态变量。

# Reading the Value of a Variable
---
在大多数情况下，可以通过`%`运算符将变量名括起来读取变量值。下面的示例将变量 `foo` 的当前值打印到控制台输出。  

```bash
C:\> SET foo=bar
C:\> ECHO %foo%
bar
``` 

在一些特殊的情况下，变量不使用 `%` 这种语法，我们将在本系列后面讨论这些特殊情况。

# Listing Existing Variables
--- 
`SET` 命令不加参数，会输出所有的变量到控制台。这些变量大多数是环境变量，比如 `%PATH%` 或者 `%TEMP%`。  

![无参数的SET命令](http://upload-images.jianshu.io/upload_images/1335634-7c9b379f146d1def.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

*无参数的SET命令*  
{:.image-caption}   
>**注意**：调用SET将列出当前会话的所有常规（静态）变量。不包括动态环境变量，如 `%DATE%`或 `%CD%`。在SET帮助文本的末尾列出这些动态变量，可以通过调用 `SET /?` 来查看。

>`%CD%` - 扩展到当前目录字符串。  
>`%DATE%` - 用跟 DATE 命令同样的格式扩展到当前日期。  
>`%TIME%` - 用跟 TIME 命令同样的格式扩展到当前时间。  
>`%RANDOM%` - 扩展到 0 和 32767 之间的任意十进制数字。  
>`%ERRORLEVEL%` - 扩展到当前 ERRORLEVEL 数值。  
>`%CMDEXTVERSION%` - 扩展到当前命令处理器扩展版本号。  
>`%CMDCMDLINE%` - 扩展到调用命令处理器的原始命令行。  
>`%HIGHESTNUMANODENUMBER%` - 扩展到此计算机上的最高 NUMA 节点号。

# Variable Scope (Global vs Local)
---
