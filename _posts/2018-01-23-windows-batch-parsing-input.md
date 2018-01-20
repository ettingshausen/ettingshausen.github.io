---
layout: cayman
title:  "Windows 批处理脚本指南:  解析输入"
date:   2018-01-21 03:01:56 +0800
categories: cmd
published: true
excluded: true
author: ettingshausen
---

>
+ [Overview]({% post_url 2017-12-09-guide-to-windows-batch-scripting %})
+ [Part 1 – 开始]({% post_url 2017-12-10-windows-batch-getting-started %})
+ [Part 2 – 变量]({% post_url 2017-12-12-windows-batch-variables %})
+ [Part 3 – 返回值]({% post_url 2017-12-17-windows-batch-return-codes %})
+ [Part 4 – 标准输入输出]({% post_url 2017-12-19-windows-batch-stdin-stdout-stderr %})
+ [Part 5 – If 语句]({% post_url 2017-12-20-windows-batch-if-then-conditionals %})
+ [Part 6 – 循环语句]({% post_url 2018-01-21-windows-batch-loops %})
+ [Part 7 – 函数]({% post_url 2018-01-22-windows-batch-functions %})
+ Part 8 – 解析输入
+ Part 9 – Logging
+ Part 10 – Advanced Tricks

健壮的输入解析，是一个好的脚本和普通脚本的区分标准。本文将介绍一些相关技巧。

# 最简单的方式读取命令行参数 
----

到目前为止，解析命令行参数最简单的方法是按序号位置读取所需参数。

在这个示例中，先拿到到第一个参数，作为传递的文件的完整路径。如果文件不存在则输出一个错误信息到标准错误输出，最后退出脚本：

```bash
SET filepath=%~f1

IF NOT EXIST "%filepath%" (
    ECHO %~n0: file not found - %filepath% >&2
    EXIT /B 1
)

```

# 可选参数
---

给参数设置默认值

```bash
SET filepath=%dp0\default.txt

:: the first parameter is an optional filepath
IF EXIST "%~f1" SET filepath=%~f1
```

# 读取用户输入
----

```bash
@ECHO OFF
:confirm
SET /P confirm="Continue [y/n]>"
ECHO %confirm% | FINDSTR /I "y" > NUL && GOTO confirm
```

使用`SET /P` 读取用户输入，然后使用`|`（管道操作符）将用户输入定向到 `FINDSTR`， `/I`表示忽略大小写。如果输入的是`Y`或者`y`，程序重复运行。
