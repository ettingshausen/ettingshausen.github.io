---
layout: cayman
title:  "Windows 批处理脚本指南: If 语句"
date:   2017-12-20 19:23:16 +0800
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
+ Part 5 – If 语句
+ [Part 6 – 循环语句]({% post_url 2018-01-21-windows-batch-loops %})
+ [Part 7 – 函数]({% post_url 2018-01-22-windows-batch-functions %})
+ Part 8 – Parsing Input
+ Part 9 – Logging
+ Part 10 – Advanced Tricks




计算机只在乎0和1是吧？所以我们需要一种方法，来处理当条件是0的时候干什么，条件为1的时候又干什么。

好消息是 DOS 对条件语句支持的非常好。

# 检查文件或文件夹是否存在
---

```bash
IF EXIST "temp.txt" ECHO found
```

取反: 

```bash
IF NOT EXIST "temp.txt" ECHO not found
```

If 和 Else 语句：

```bash
IF EXIST "temp.txt" (
    ECHO found
) ELSE (
    ECHO not found
)
```

>NOTE: 在判断的表达式两边加上双引号，这样可以避免一些bug，比如变量不存在，导致语法错误。

# 检查变量是否初始化
----

```bash
IF "%var%"=="" (SET var=default value)
```

或者

```bash
IF NOT DEFINED var (SET var=default value)
```

# 检查变量是否与字符串匹配

----

```bash
SET var=Hello, World!

IF "%var%"=="Hello, World!" (
    ECHO found
)
```

或者不区分大小写来比较：

```bash
SET var=Hello, World!

IF /I "%var%"=="hello, world!" (
    ECHO found
)
```

# 算数运算符比较
----

```bash
SET /A var=1

IF /I "%var%" EQU "1" ECHO equality with 1

IF /I "%var%" NEQ "0" ECHO inequality with 0

IF /I "%var%" GEQ "1" ECHO greater than or equal to 1

IF /I "%var%" LEQ "1" ECHO less than or equal to 1
```

# 检查返回值

----

```bash
IF /I "%ERRORLEVEL%" NEQ "0" (
    ECHO execution failed
)
```