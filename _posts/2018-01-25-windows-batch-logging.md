---
layout: cayman
title:  "Windows 批处理脚本指南: 日志"
date:   2018-01-21 16:34:11 +0800
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
+ Part 9 – 日志
+ Part 10 – Advanced Tricks

我在脚本中使用基本日志记录工具，来捕获脚本执行期间或者执行之后的故障信息。使用基本日志记录来检测脚本在运行时正在做什么，以及为什么它这么做。我记得有一个网络操作中心试图解决一个遗留批处理的问题，在这个过程中，系统管理员通常必须尝试读取一个控制台窗口的行，因为它们是滴流而过。当批处理机使用拨号调制解调器连接到远程资源时，在很长一段时间里这种技术工作得很好。然而，宽带的出现意味着批脚本的执行速度超过了任何人能够读取的速度。简单的日志文件将使这些系统管理员的故障排除工作更加容易。

# 日志函数
---

在[Part 7 – 函数]({% post_url 2018-01-22-windows-batch-functions %})中，示范了`tee`的简单版实现

```bash
@ECHO OFF
SETLOCAL ENABLEEXTENSIONS

:: script global variables
SET me=%~n0
SET log=%TEMP%\%me%.txt

:: The "main" logic of the script
IF EXIST "%log%" DELETE /Q %log% >NUL

:: do something cool, then log it
CALL :tee "%me%: Hello, world!"

:: force execution to quit at the end of the "main" logic
EXIT /B %ERRORLEVEL%

:: a function to write to a log file and write to stdout
:tee
ECHO %* >> "%log%"
ECHO %*
EXIT /B 0
```

这个`tee`函数将Console中的输出同时写入了一个日志文件。在这里，重用了相同的日志文件路径，保存在用户`%TEMP%`文件夹中。如果需要为每个执行保留日志，可以通过`%DATE%`或者`%TIME%`来创建一个唯一的日志文件。 

```bash
REM create a log file named [script].YYYYMMDDHHMMSS.txt
SET log=%TEMP%\%me%.%DATE:~10,4%_%DATE:~4,2%_%DATE:~7,2%%TIME:~0,2%_%TIME:~3,2%_%TIME:~6,2%.txt
```

在消息的内容前加上一个前缀，例如`script: some message`。这个技巧可以在出错的情况下，快速发现是在哪个地方输出的异常消息。

# 显示启动参数
----

显示非交互式脚本的各种运行时条件，比如在构建服务器上运行的一些东西，然后重定向到构建日志。遗憾的是，我还不知道有什么DOS技巧(但是)可以区分非交互式会话和交互式会话。 C# 和.Net有个`System.Environment.UserInteractive`属性可以检测这种情况。Unix有一些带有tty文件描述符的技巧。你可能需要监测一个自定义的环境变量来破解实现，比如`%MYSCRIPT_DEBUG%`默认值是false。