---
layout: cayman
title:  "Windows 批处理脚本指南: 返回值"
date:   2017-12-17 21:12:01 +0800
categories: cmd
published: true
excluded: true
author: ettingshausen
--- 

>
+ [Overview]({% post_url 2017-12-09-guide-to-windows-batch-scripting %})
+ [Part 1 – 开始]({% post_url 2017-12-10-windows-batch-getting-started %})
+ [Part 2 – 变量]({% post_url 2017-12-12-windows-batch-variables %})
+ Part 3 – 返回值
+ [Part 4 – 标准输入输出]({% post_url 2017-12-19-windows-batch-stdin-stdout-stderr %})
+ [Part 5 – If 语句]({% post_url 2017-12-20-windows-batch-if-then-conditionals %})
+ [Part 6 – 循环语句]({% post_url 2018-01-21-windows-batch-loops %})
+ [Part 7 – 函数]({% post_url 2018-01-22-windows-batch-functions %})
+ [Part 8 – 解析输入]({% post_url 2018-01-23-windows-batch-parsing-input %})
+ [Part 9 – 日志]({% post_url 2018-01-25-windows-batch-logging %})
+ Part 10 – Advanced Tricks  
 


今天来讲一下脚本的返回值，返回值是与外界沟通的唯一方式。 然而，即使熟练的Windows程序员也忽略了返回码的重要性。


# Return Code Conventions
---
通常情况下，命令行程序执行成功会返回 0 ，执行错误会返回非0的数字。 警告信息则不影响返回值。重要的是脚本是否工作。 


# Checking Return Codes In Your Script Commands
---
`%ERRORLEVEL%` 环境变量存储了上一次程序返回的代码。`ECHO`、`IF`和`SET`将保留`%ERRORLEVEL%`的现有值，这是一个飞非常有用的特性。 
常用的做法是使用`IF`语句中的`NEQ`(不等于)运算符来检查脚本的返回值：
```bash
IF %ERRORLEVEL% NEQ 0 (
    REM do something here to address the error
)
```  

还有一种做法：
```bash
IF ERRORLEVEL 1 (
    REM do something here to address the error
)
```
如果脚本的返回值大于1，则 `ERRORLEVEL 1`表达式为真。 最好不使用这种方法，有时候脚本也会返回一些负数。

有时可能需要检查脚本返回的具体值， 例如测试一个可执行程序是否加入了`PATH`环境变量，可以判断返回代码是否为 `9009`
```bash
SomeFile.exe
IF %ERRORLEVEL% EQU 9009 (
    ECHO error -SomeFile.exe not found in your PATH
)
```
事先很难知道返回的究竟是什么，通常是使用一些错误的试验来确定返回的究竟是什么。记住，这不是一种很好的编程方式，一点也不完美，但是它能解决问题 :joy: 

# Conditional Execution Using the Return Code
---
有一种非常炫酷的方法，根据第一条语句执行的结果来执行随后的语句。前边的程序或者脚本必须返回成功（0）或者失败（非0）的标志。

在脚本执行成功后执行跟随的命令，可以使用`&&`操作符：
```bash
SomeCommand.exe && ECHO SomeCommand.exe succeeded!
``` 
在脚本执行失败后执行跟随的命令，可以使用`||`操作符：
```bash
SomeCommand.exe || SomeCommand.exe failed with return code %ERRORLEVEL%
```  

默认情况下，当发生错误时，命令处理器还会继续执行。 你必须编写代码停止它。 

停止程序非常简单的一个做法是使用`EXIT`命令和`/B` 开关（退出当前运行的脚本，而不是命令提示符窗口，如果不加`/B`，会退出整个命令提示符窗口）。同时传递一个非零的数字通知调用者脚本执行失败了。
 ```bash
 SomeCommand.exe || EXIT /B 1
 ```

还有一种方法是使用`GOTO`跳转至 `:EOF`(End-Of-File)。跳转到脚本末，脚本将结束，并返回 1。
```bash
SomeCommand.exe || GOTO :EOF
``` 

# Tips and Tricks for Return Codes
建议坚持使用`0`作为脚本成功执行的返回值。正数作为失败返回。这样就能使用`IF ERRORLEVE 1` 的用法来判断脚本是否正常执行了。同时，也建议使用`SET` 命令，在脚本的顶部定义错误的返回值，增强可读性，例如：
```bash
SET /A ERROR_HELP_SCREEN=1
SET /A ERROR_FILE_NOT_FOUND=2
``` 
>**Note:** 之前讲过，变量名最好使用小写，但是因为DOS不支持常量，这里使用大写目的是为了表示，这些是不经常改动的常量。 


# Some Final Polish
---
一个小技巧，返回的值是2的N次幂。
```bash
SET /A ERROR_HELP_SCREEN=1
SET /A ERROR_FILE_NOT_FOUND=2
SET /A ERROR_FILE_READ_ONLY=4
SET /A ERROR_UNKNOWN=8
``` 
如果需要记录大量的错误信息在一个返回值，可以通过按位或，将错误编码加在一起。

```bash
@ECHO OFF
SETLOCAL ENABLEEXTENSIONS

SET /A errno=0
SET /A ERROR_HELP_SCREEN=1
SET /A ERROR_SOMECOMMAND_NOT_FOUND=2
SET /A ERROR_OTHERCOMMAND_FAILED=4

SomeCommand.exe
IF %ERRORLEVEL% NEQ 0 SET /A errno^|=%ERROR_SOMECOMMAND_NOT_FOUND%

OtherCommand.exe
IF %ERRORLEVEL% NEQ 0 (
    SET /A errno^|=%ERROR_OTHERCOMMAND_FAILED%
)

EXIT /B %errno%

``` 

如果`SomeCommand.exe `和`OtherCommand.exe` 都失败了，返回值将是6，这样就能根据6推断出，哪些步骤出现了错误。