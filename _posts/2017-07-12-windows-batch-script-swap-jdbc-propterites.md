---
layout: post
title:  "Windows批处理切换jdbc配置文件"
date:   2017-07-12 19:03:06 +0800
categories: Windows
---


Java Web 开发中经常要切换数据库，还比较频繁。每切换一次就要重新配置一次 `jdbc.properites` 实在是麻烦。当然，maven支持多种配置，但是，速度太慢，切换一下得刷新一会儿。通过批处理的方式就快多了。

先看下 `jdbc.properties` 的内容

```properties
# 
#Copyright © 2017 NPKW
#  
jdbc.driverClassName=com.microsoft.sqlserver.jdbc.SQLServerDriver  
jdbc.url=jdbc:sqlserver://localhost;DatabaseName=Database  
jdbc.username=sa 
jdbc.password=password

```

前三行是注释，真正有用的是后面的4行。以切换本地的数据库与服务端的数据库为例，`jdbc.properties` 中的区别只是最后的三行。很自然的想到，只要能把最后的三行替换掉就行了。顺着这个思路，用批处理脚本，替换掉最后三行。实际操作的时候发现，要控制读取替换具体的某一行还是比较麻烦的操作。

考虑这个文件只有几行，可以直接将其全部覆盖。只需要将每次的配置用批处理输出到配置文件就可以了。

```bash
@echo off
SETLOCAL

set Comment=#
set Copyright=Copyright © 2017 NPKW
set Driver=jdbc.driverClassName=com.microsoft.sqlserver.jdbc.SQLServerDriver
set LocalHost=jdbc.url=jdbc:sqlserver://localhost;DatabaseName=Database
set UserName=jdbc.username=sa
set Password=jdbc.password=password

(echo %Comment% & echo %Comment%%Copyright%  & echo %Comment%  & echo %Driver%  & echo %LocalHost%  & echo %UserName% & echo %Password%) > jdbc.properties
```

`SETLOCAL` 的意思是脚本中定义的变量，只在当前会话中有效，脚本执行完成之后，变量就会被释放掉。为了不污染系统的环境变量，最好先使用 `SETLOCAL`。 然后，把将要组合的文本存储在各个变量中。 最后拼接各个变量输出到jdbc.properties。注意使用 `>` 符号是覆盖， `>>` 是追加。两个取出的字符不需要任何连接符，比如 `%Comment%%Copyright%`。

回过头来看，批处理的变量的使用方式， 不需要声明，又有点像 T-SQL 语法。而且通过 /A 参数，可以用一个表达式初始化一个变量。例如： `set /a sum=1+1` , sum 的值为2。 特别注意的地方，默认初始化变量， `=` 两边不要有空格。 例如 `set b = 1` ，用 `echo %b%` 输出得到是 `%b%` 而不是 1。

一开始曾想用 Powershell 去做，Powershell 非常的强大，几乎支持 .NET 的所有操作，但是 IDEA Intelij 不支持 Powershell 直接运行， 而且 Powershell 有多个版本，新的机器支持，老的机器或许就不支持了。 批处理脚本则不一样，虽然功能看起来弱了些，但是非常的稳定，兼容性也非常好，现在市面上的 Windows 系统都能够支持它。




