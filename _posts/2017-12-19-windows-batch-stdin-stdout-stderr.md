---
layout: cayman
title:  "Windows 批处理脚本指南: 标准输入输出"
date:   2017-12-19 19:23:16 +0800
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
+ Part 4 – 标准输入输出
+ [Part 5 – If 语句]({% post_url 2017-12-20-windows-batch-if-then-conditionals %})
+ Part 6 – Loops
+ Part 7 – Functions
+ Part 8 – Parsing Input
+ Part 9 – Logging
+ Part 10 – Advanced Tricks    

与Unix/Linux 一样，DOS使用三个通用的“文件流”来进行输入，输出文本信息或者错误信息。程序或者脚本使用标准输入文件（stdin）读取内容，标准输出（stdout）输出文件或者打印文本到屏幕上，标准错误（stderr）输出错误信息到屏幕上。

# File Numbers
---

这三个标准文件（也称为标准流）使用数字 0，1，2 进行标记。标准输入是文件0，标准输出是文件1，标准错误是文件2。下文中的文件流重定向会用到这三个数字。

# Redirection[^redirection]
---

批处理脚本经常需要把一些程序的日志输出的一个文本文件。 `>` 操作符可以将标准输出或者错误重定向到一个文件。比如，列出当前目录下的文件信息，并存储到一个文本文件，可以这么操作：
```bash
DIR > temp.txt
```

`>` 操作符会覆盖目标文件的内容， `>>` 操作符则是将内容追加至文件末。
```bash
DIR > temp.txt
DIR >> temp.txt
```  

默认情况下，`>`、`>>` 是将标准输出重定向。 也可以在操作符前加上 `2` （注意没有空格） 来重定向标准错误。
```bash
DIR SomeFile.txt  2>> error.txt
```  

也可以通过`>&`运算符，将标准输出、错误相互转换。 例如，将标准输出转为异常来输出到`error.txt`
```bash
Some.exe 2> error.txt 1>&2
```


通过几个例子看下， 将下边的内容保存为 `test.cmd`
```bash
@ECHO OFF
ECHO Hello
some.exe
``` 


1. 使用默认输出，`test.cmd > log.txt`  
>**log.txt :**  
    Hello   

    `log.txt` 只包含了正常的消息内容， 屏幕上输出了错误信息：
    >'some.exe' 不是内部或外部命令，也不是可运行的程序或批处理文件。

2. 使用 `test.cmd 1> log.txt` 输出
>**log.txt :**  
    Hello   

    同上，`log.txt` 同样包含了正常的消息内容，屏幕上输出了错误消息。 说明，默认的`>` 与 `1>` 相同。

3. 使用 `test.cmd 2> log.txt` 输出
>**log.txt :**  
    'some.exe' 不是内部或外部命令，也不是可运行的程序或批处理文件。   

    `log.txt` 只输出了异常的消息内容，屏幕输出了正常的消息。

4.  使用 `test.cmd > log.txt 1>&2` 输出
>**log.txt :**  
  
    `log.txt`的内容为空白，屏幕上输出了正常与错误的消息。  这行命令的作用是将`stdout`当作`stderr`处理，并把`stdout`输出到`log.txt`，所以`stdout`显示在了屏幕上，`log.txt`为空白。

5.  使用 `test.cmd 2> log.txt 1>&2` 输出
>**log.txt :**  
    Hello  
    'some.exe' 不是内部或外部命令，也不是可运行的程序或批处理文件。
  
    `log.txt`包含了正常与错误的消息。 这行命令的作用是将`stdout`当作`stderr`处理，并将`stderr`输出到`log.txt`  



使用 `<` 运算符可以将文件内容读入程序或者脚本。例如：
```bash
SORT < SomeFile.txt
```  
`SomeFile.txt`的内容为:
>   2  
    4  
    1  
    3

```bash
SORT < SomeFile.txt > result.txt
``` 
`result.txt`的内容为：
>    1  
     2  
     3  
     4  

# Suppressing Program Output  
---

`NUL`是一个虚拟的设备（文件），将`stdout`重定向到`NUL`，则会丢弃标准输出。例如：
```bash
ping 127.0.0.1 > NUL
``` 
屏幕上不会输出任何内容。在比如屏蔽掉错误输出，以上文中的`test.cmd`脚本为例，
```bash
test.cmd 2> NUL
```
>Hello  
  
只输出了 Hello， 错误的内容并没有输出。   


# Redirecting Program Output As Input to Another Program
---
如果要把一条命令的输出作为另外一条命令的输入，可以借助`|`操作符来完成。例如将当前目录下的文件排序：
```bash
DIR /B | SORT
```
倒序：
```bash
DIR /B | SORT /R
```


# A Cool Party Trick
---
有个技巧可以在命令提示符窗口中创建文本文件或者脚本，通过`CON`将命令提示符自己的输入重定向到一个文件。输入完成后需要按下`Ctrl` + `C`， 发送一个结束符（EOF）。

```bash
TYPE CON > output.txt
```  

类似的技巧还有许多，比如新建一个空文本：
```bash
TYPE NUL > w.txt
```



在DOS上还有一些其他的特殊设备可以重定向，但是大多数有点类似于LPT1（用于并行端口打印机）和COM1（用于串口设备，如调制解调器）。
# 参考资料
---
[^redirection]:[redirection operators](https://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/redirection.mspx?mfr=true)



