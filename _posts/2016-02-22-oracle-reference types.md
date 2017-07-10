---
layout: post
title:  "Oracle PL/SQL引用数据类型"
date:   2016-02-22 10:05:06 +0800
categories: Oracle
---

Oracle的引用数据类型，是指定义一个变量，这个变量的类型和某张表中的的一个字段类型一样，这样做的好处是，当表中这个字段的数据类型发生变化时，引用的数据类型也会跟着改变。
```sql 
declare
pename --变量名  emp.ename%type -- 变量类型;
```
[示例](http://www.imooc.com/video/6982)：
```sql
--引用型变量  
--打开oracle的输出口  
--set serveroutput on  
declare  
    --定义引用型变量，查询并打印1232的姓名和薪水  
    --pename varchar2(20);--这2句和下面的2句效果一致  
    --psal number;  
    pename emp.ename%type;  
    psal emp.sal%type;  
begin  
    --得到1232的姓名和薪水  
    --赋值的方式有:=和into  
    select ename,sal into pename,psal from emp where empno=122;  
    --打印姓名和薪水  
    dbms_output.put_line(pename||'的薪水是'||psal);  
end;  
/  
```