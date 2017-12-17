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

`/A` 开关支持算数操作。这是一个有用的工具，如果需要验证该用户输入是一个数值。 
```bash
SET /A four=2+2
4
```  
一般的惯例，变量使用小写名称，系统变量（环境变量）一般是大写。这些环境描述了在系统中哪里可以找到某些东西，例如 `%TEMP%`，它是临时文件的路径。DOS是不区分大小写的，所以这个惯例并不是强制的，但是让脚本更容易阅读或者故障排除，倒是个好主意。
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
默认情况下，变量对整个命令提示符会话是全局的。调用`SETLOCAL`命令，将变量变为局部变量。任何局部变量赋值在调用`ENDLOCAL`，`EXIT`，或者当执行到达脚本中的文件结尾（EOF）时都会恢复。 

本示例演示如何在名为`HelloWorld.cmd`的脚本中更改名为`foo`的现有变量。 当脚本退出时，`%foo%`会恢复的原始值。
>`HelloWrold.cmd`
```bash
SETLOCAL
SET v=Local Value
ECHO %v%
```   

![变量恢复](http://upload-images.jianshu.io/upload_images/1335634-51ada57b0ee67159.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
*变量恢复*  
{:.image-caption}  

一个真实的例子应该是修改系统的`%PATH%`环境变量。
> **PATH**: 存储了执行命令时搜索的目录列表。  

![修改系统Path环境变量](http://upload-images.jianshu.io/upload_images/1335634-aefd4cfb99e859e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*修改系统Path环境变量*  
{:.image-caption}   


# Special Variables 
---

有些特殊情况，变量的使用有些小差异。在命令行中给脚本传递的参数也是变量，但是不要使用`%var%`语法。 而是用`%`跟一个0-9的数字读取每个参数，数字代表参数的位置。 在稍后的创建函数的例子中，将会看到同样的风格。  

还有一个使用`!`的变量语法，就像`!var!` 这是一种称为延迟扩展的特殊情况。 当我们讨论条件（if / then）和循环时，将会讲解更多关于延迟扩展的内容。  

# Command Line Arguments to Your Script
---
你可以使用特殊的语法读取传递给脚本的命令行参数。 语法是一个单一的`%`字符，后面是从0到9的参数的序号位置。零序参数是批处理文件本身的名称。 所以我们脚本`HelloWorld.cmd`中的变量`%0`将是“HelloWorld.cmd”。  

命令行的第一个非空参数就是 `%1`, 第二个就是`%2` ……， 第九个就是`%9`。

>**Note:** DOS确实支持9个以上的命令行参数，然而，你不能直接读取第9个之后参数。这是因为特殊变量语法不能识别`%10`或更高。 实际上`%10`，它只识别到`%1`, `0` 作为一个字符被拼接到后边。

通过一个例子来测试下，写几个脚本命名为 `arguments.cmd`， 内容如下
```bash
echo %1
echo %10
``` 
![不能识别%10](http://upload-images.jianshu.io/upload_images/1335634-5245aabd35a0967d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
*不能识别%10*  
{:.image-caption}   

从运行结果来看，`%10`实际上是： 第一个参数 ＋ `0` 。  

使用`SHIFT`命令从参数列表中弹出第一个参数，这使得所有的参数都向左移动，这样第十个参数就可以通过`%9`来获取了。在循环的部分，将会详细讲解这部分内容。 

# Tricks with Command Line Arguments
---

命令行参数还支持一些非常有用的可选语法，针对文件路径参数，可以解析作为命令行参数的文件的路径、时间戳或大小。这个超级有用的特性文档有点难找 :pouting_cat:，运行 `for /?`，在页面的最末尾。

+ `%~I`从第`I`个命令行参数中删除引号，在处理文件路径参数时非常有用。之前讲过，带空格的路径需要用引号括起来，但是多次括起来就会导致错误。这里的`I`可以事`0~9`的整数。

+ `%~fI` 展开完整路径 
例如 `args.cmd`：
```bash
echo %~f1
```  
调用
```bash
args.cmd .\adoble
``` 
输出：`C:\Users\Edward\Desktop\Adobe`  

+ `%~fsI` 与上边的类似，`s` 选项会生成一个 DOS 8.3[^8-3filename]的短路径，例如：`C:\Program Files`缩写为 `C:\PROGRA~1`。在使用一些不处理空格的第三脚本，这个技巧很实用啊。 :wink:  

+ `%~dpI` 第`I`个文件路径参数的完整父级路径。几乎在每个批处理脚本中，我都用这个技巧来确定脚本的位置。 `SET parent=%~dp0`通过这个用法，可以输出脚本的所在路径。

+ `%~nxI` 第`I`个文件路径参数的文件名（包括扩展名）。类似`%0`， 我也经常使用这个技巧来确定运行时脚本的名字。如果需要输出消息给用户，我喜欢在消息前面加上脚本的名字，像这样`ECHO %~n0: 输出的消息`，而不是直接输出`ECHO 输出的消息`。这样做的好处是，用户知道这个消息是从哪个脚本中输出的。如果你花了几个小时的时间为了确定错误消息是从哪个脚本输出的，是不是很崩溃？:laughing:


# Some Final Polish
---
我总是将这些命令包含在批处理脚本的顶部：
```bash
SETLOCAL ENABLEEXTENSIONS
SET me=%~n0
SET parent=%~dp0
``` 
`SETLOCAL`命令能保证脚本在退出之后不覆写任何现有的变量。`ENABLEEXTENSIONS`[^enableextensions]，是`SETLOCAL`的一个参数，这是一个非常有用的特性，称之为`启动或停用命令处理器扩展名`。别问为什么真的很有用 :joy:  
`me`变量存储了脚本的名称（不包含扩展名）。这样就能很方便的给输出的消息加上前缀`ECHO %me%: 输出的消息`。  
`parent`存储了脚本的所在目录，这样可以很容易的给同一目录下的其他文件拼接出完整的路径。

# 参考资料
---

[^8-3filename]: [8.3 filename](https://en.wikipedia.org/wiki/8.3_filename)  
[^enableextensions]: [Determines whether the extensions to the command processor (Cmd.exe)](https://technet.microsoft.com/en-us/library/cc959665.aspx)
