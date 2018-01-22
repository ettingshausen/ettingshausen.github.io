---
layout: cayman
title:  "Windows 批处理脚本指南:  函数"
date:   2018-01-21 01:54:23 +0800
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
+ Part 7 – 函数
+ [Part 8 – 解析输入]({% post_url 2018-01-23-windows-batch-parsing-input %})
+ [Part 9 – 日志]({% post_url 2018-01-25-windows-batch-logging %})
+ [Part 10 – 高级技巧]({% post_url 2018-01-26-windows-batch-advanced-tricks %})

函数实际上是在任何过程编码语言中重用代码的方法。然而DOS缺乏真正的功能关键字，但是你可以提供过`CALL` 关键字来实现。

有两个需要注意的问题：

1. “函数”需要在脚本的底部通过标签来定义
1. 脚本的主要逻辑（主函数）需要 `EXIT /B [errorcode]` 这样可以组织主逻辑进入函数。

# 定义一个函数
----
在本例中，我们将实现一个简单版的*nix tee[^tee]实用程序，以将消息写入到文件和stdout流中。我们将在整个脚本中使用一个全局变量，在函数中使用`%log%`。

```bash
@ECHO OFF
SETLOCAL

:: script global variables
SET me=%~n0
SET log=%TEMP%\%me%.txt

:: The "main" logic of the script
IF EXIST "%log%" DEL /Q %log% >NUL

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

# 调用函数
----
使用`CALL`关键字来调用函数。可以传递命令行参数，就像调用另一批处理文件一样。必须记住在结束时`EXIT /B`关键字。遗憾的是，除了退出代码之外，什么都不能返回。

# 返回值
----

调用的返回值始终是函数的退出代码。与任何可执行文件的调用一样，调用者读取`%ERRORLEVEL%`获得退出代码。除了整数返回代码以外，你必须用其他创造性的方式来输出。可以`ECHO`标准输出，让调用者决定通过将输出连接到另一个可执行文件、重定向到文件或通过`FOR`命令解析输出。

调用者也可以通过修改全局变量传递数据，不过，尽量避免这种方法。

# 参考资料
---

[^tee]: [tee](https://en.wikipedia.org/wiki/Tee_(command))是一个常见的指令，它能够将某个指令的标准输出，导向、存入某个档案中