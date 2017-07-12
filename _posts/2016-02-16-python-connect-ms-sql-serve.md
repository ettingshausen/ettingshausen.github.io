---
layout: post
title:  "Python连接MS SQL Server"
date:   2016-02-16 16:05:06 +0800
categories: Python
---

Python访问各个数据库需要需要借助对应的modules，比如MySQL需要[MySQLdb](https://dev.mysql.com/downloads/connector/python/)，SQL Server需要[pymssql](http://www.pymssql.org/en/latest/index.html)。
两个模块大同小异，都遵循[Python Database API](https://www.python.org/dev/peps/pep-0249/)


##Python Database API
Python Database API，只需要了解[Connection Objects](https://www.python.org/dev/peps/pep-0249/#connection-objects)和[Cursor Objects](https://www.python.org/dev/peps/pep-0249/#cursor-objects)的常用方法。


####Connection Objects
| 方法|含义  |
| -  |:-: |
|[cursor ](https://www.python.org/dev/peps/pep-0249/#cursor)| 返回一个Cursor对象 |
| [commit ](https://www.python.org/dev/peps/pep-0249/#commit)| 提交事务  | 
| [rollback ](https://www.python.org/dev/peps/pep-0249/#rollback)| 回滚| 
|[close](https://www.python.org/dev/peps/pep-0249/#Connection.close)|关闭连接|

####Cursor Objects
|方法|含义|
|-|:-:|
|[execute ](https://www.python.org/dev/peps/pep-0249/#id15)|执行一条SQL语句|
|[executemany ](https://www.python.org/dev/peps/pep-0249/#executemany)|执行多条语句|
|[fetchone ](https://www.python.org/dev/peps/pep-0249/#fetchone)|获取一行数据|
|[fetchmany ](https://www.python.org/dev/peps/pep-0249/#fetchmany)|获取n行的数据|
|[fetchall ](https://www.python.org/dev/peps/pep-0249/#fetchall)|获取未返回的数据|
|[close ](https://www.python.org/dev/peps/pep-0249/#Cursor.close)|关闭游标|
了解了Python Database API值之后安装[pymssql](http://www.pymssql.org/en/latest/index.html#pymssql)
安装好之后开工了。
**如果是连接本地的SQL Server需要在 SQL Server Configuration 中打开TCP/IP协议**

![ SQL Server Configuration](http://upload-images.jianshu.io/upload_images/1335634-93526fa3dfa8f525.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

代码如下：
```python
#coding=utf-8
import pymssql
conn = pymssql.connect(host='127.0.0.1',user='sa',
                       password='hello',database='NPKW',
                      charset="utf8")
#查看连接是否成功
print conn
cursor = conn.cursor()
sql = 'select * from contacts'
cursor.execute(sql)
#用一个rs变量获取数据
rs = cursor.fetchall()
print rs
```
输出如下：
><pymssql.Connection object at 0x02FF2CB0>
><pymssql.Cursor object at 0x03124110>
>[(1, u'20111612210028', u'ettingshausen', u'', u'', u'', u'', u'', u'', u'', u'', u'')]