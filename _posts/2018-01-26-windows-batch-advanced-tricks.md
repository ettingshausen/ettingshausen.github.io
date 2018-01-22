---
layout: cayman
title:  "Windows 批处理脚本指南: 高级技巧"
date:   2018-01-22 11:38:26 +0800
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
+ [Part 8 – 解析输入]({% post_url 2018-01-23-windows-batch-parsing-input %})
+ [Part 9 – 日志]({% post_url 2018-01-25-windows-batch-logging %})
+ Part 10 – 高级技巧

# 模板信息
-----

我喜欢在脚本的顶部添加一段文档，用来描述 who/what/when/why/how。你可以使用`::`技巧，是文档的可读性更高。

```bash
:: Name:     MyScript.cmd
:: Purpose:  Configures the FooBar engine to run from a source control tree path
:: Author:   stevejansen_github@icloud.com
:: Revision: March 2013 - initial version
::           April 2013 - added support for FooBar v2 switches

@ECHO OFF
SETLOCAL ENABLEEXTENSIONS ENABLEDELAYEDEXPANSION

:: variables
SET me=%~n0


:END
ENDLOCAL
ECHO ON
@EXIT /B 0

```

# 基于成功/失败的条件命令
----

使用`||`和`&&`逻辑运算符，可以方便的将两行命令写在一行。

使用`&&`连接的两条命令，当第一条执行成功之后（`%ERRORLEVEL%`是0），紧接着会执行第二条命令。

```bash
DIR myfile.txt >NUL 2>&1 && TYPE myfile.txt
```

`||`则相反，当第一条命令执行失败后，执行第二条命令。

```bash
DIR myfile.txt >NUL 2>&1 || CALL :WARNING file not found - myfile.txt
```

也可以把这两个技巧结合起来使用，通过`()`来构造，当第一条执行失败后，执行`()`中的两条语句。

```bash
DIR myfile.txt >NUL 2>&1 || (ECHO %me%: WARNING - file not found - myfile.txt >2 && EXIT /B 1)
```


# 获取脚本文件的父级目录完整路径
----

```bash
:: variables
PUSHD "%~dp0" >NUL && SET root=%CD% && POPD >NUL
```

# 让脚本暂停
----

可以通过`ping.exe`来模拟一个类似UNIX的`sleep`功能。

```bash
:: sleep for 2 seconds
PING.EXE -N 2 127.0.0.1 > NUL
```

# 支持“双击”执行(从Windows资源管理器调用)
----

测试一下`%COMSPEC%`与`%CMDCMDLINE%`是否相同，如果相同，那就可以认为，脚本是运行在一个交互的会话中。如果不相同，我们需要在脚本的末尾加上`PAUSE`命令，用来演示的结果，否则命令提示符窗口在脚本运行结束会自动关闭。

```bash
@ECHO OFF
SET interactive=0

ECHO %COMSPEC% | FINDSTR /L %CMDCMDLINE% >NUL 2>&1
IF %ERRORLEVEL% == 0 SET interactive=1

ECHO do work

IF "%interactive%"=="0" PAUSE
EXIT /B 0
```