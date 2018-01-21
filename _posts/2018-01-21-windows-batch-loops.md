---
layout: cayman
title:  "Windows 批处理脚本指南:  循环语句"
date:   2018-01-21 00:14:20 +0800
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
+ Part 6 – 循环语句
+ [Part 7 – 函数]({% post_url 2018-01-22-windows-batch-functions %})
+ [Part 8 – 解析输入]({% post_url 2018-01-23-windows-batch-parsing-input %})
+ [Part 9 – 日志]({% post_url 2018-01-25-windows-batch-logging %})
+ Part 10 – Advanced Tricks

在集合中遍历条目是脚本的常见任务。它可以遍历目录中的文件，或者一次读取一行文本。

# 传统的GOTO语句
-----
早期版本的DOS的老方法是使用标签和GOTO语句。虽然它对于通过命令行参数循环很有用，但现在已经不再使用了。

```bash
:args
SET arg=%~1
ECHO %arg%
SHIFT
GOTO :args
```

# FOR
-----

遍历文件或者文本更现代的方法是使用`for`命令。在我看来，`for`是DOS最强大的命令，也是最不常使用的命令之一。

FOR命令使用一个特殊的变量语法`%`，后跟一个字母，如`%I`。当批处理文件中使用此语法时，略有不同，需要两个百分号`%%I`。在编写脚本时，这是一个常见的错误来源。如果for循环因为语法错误退出，确认是否使用了`%%I`。

# 遍历文件
----

```bash
FOR %I IN (%USERPROFILE%\*) DO @ECHO %I
```

# 遍历文件夹
----

```bash
FOR /D %I IN (%USERPROFILE%\*) DO @ECHO %I
```

递归遍历`%TEMP%`下所有的文件

```bash
FOR /R "%TEMP%" %I IN (*) DO @ECHO %I
```

递归遍历`%TEMP%`下所有的文件夹

```bash
FOR /R "%TEMP%" /D %I IN (*) DO @ECHO %I
```

# 一个例子
---

{% gist 3d0d8d5d0f9342e72a0a2f1df0846848 %}
