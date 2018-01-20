---
layout: post
title:  "Windows 批处理脚本指南"
date:   2017-12-09 22:55:45 +0800
categories: cmd
author: ettingshausen
published: true
---

Windows 10 替换了默认的 `cmd` 为 `Powershell` ，显然 `Powershell` 更为强大，但是缺点是目前速度比较慢。前半年偶然看到了 Steve Jansen 的博客，其中有一个系列是有关批处理，深入浅出，看完后收获颇丰。现将其翻译，也算是学习笔记。如有纰漏，欢迎指出。

>原文：[Guide to Windows Batch Scripting](http://steve-jansen.github.io/guides/windows-batch-scripting/) 


# Why Windows？
---
我喜欢shell脚本。 成本低，效益高。 感觉就像艺术一样，人们可以学习以更简单的方式完成越来越复杂的任务。  

不幸的是，我觉得这是一个下滑的开发者技能。 也许新的开发人员觉得这是“不是真正的编程”。 也许，Java作为计算机科学课程的通用语言日益占据主导地位，这使得大学课程设置中，脚本编程的影响力降低了。  

讲真，shell脚本感觉有点“职业化”，甚至有点不像Python / Ruby / LISP / blah / blah / blah。 然而，随着你的资历逐渐增加，在你日常工作中开始做更多的开发配置，或者你想要做一些高速，高效的量身定制你的开发环境，这种技能就变得无价了，比如[这样](https://github.com/blog/1345-introducing-boxen)。  
这系列文章将分享我在Windows专业工作多年中所学到的一些技巧和窍门。我承认UNIX shell远远优于Windows命令提示符（甚至是Windows PowerShell）。事实上，大多数专业人士使用Windows为公司的客户写代码的；这个系列旨在让Windows的使用更轻松一些。

# Why DOS-style Batch Files?
---  
这系列文章将分享一些，我自己再些批处理脚本的一些习惯。 Windows PowerShell绝对是不错的，但是，我仍然喜欢批处理文件，因为它的便携性、也没什么冲突性。 Windows命令行非常稳定 - 无需担心PowerShell解释器路径，服务器正在运行的PowerShell版本等。

# Series Parts  
---

+ [Part 1 – 开始]({% post_url 2017-12-10-windows-batch-getting-started %})
+ [Part 2 – 变量]({% post_url 2017-12-12-windows-batch-variables %})
+ [Part 3 – 返回值]({% post_url 2017-12-17-windows-batch-return-codes %})
+ [Part 4 – 标准输入输出]({% post_url 2017-12-19-windows-batch-stdin-stdout-stderr %})
+ [Part 5 – If 语句]({% post_url 2017-12-20-windows-batch-if-then-conditionals %})
+ [Part 6 – 循环语句]({% post_url 2018-01-21-windows-batch-loops %})
+ Part 7 – Functions
+ Part 8 – Parsing Input
+ Part 9 – Logging
+ Part 10 – Advanced Tricks  

:wink:
未完待续! 