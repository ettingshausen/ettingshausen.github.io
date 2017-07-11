---
layout: post
title:  "Oracle PL/SQL 从键盘接收用户输入"
date:   2016-02-22 10:05:06 +0800
categories: Oracle
---

>PL/SQL 还是比较强大的，可以用键盘接收用户输入。

```sql
/*
判断用户从键盘输入的数字
1.如何使用if语句
2.接收一个键盘输入（字符串）
*/
set serveroutput on
--接收一个键盘输入
--num:地址值，含义是：在该地址上保存了输入的值
accept num prompt '请输入一个数字';
declare
--定义变量保存用户从键盘输入的数字
pnum number:= # --&地址符号，将num地址上的值赋给pnum
begin
if pnum=0 then dbms_output.put_line('您输入的数字为0');
elsif pnum=1 then dbms_output.put_line('您输入的数字为1');
elsif pnum=2 then dbms_output.put_line('您输入的数字为2');
else dbms_output.put_line('其他数字');
end if;
end;
/
```