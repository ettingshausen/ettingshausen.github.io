---
layout: post
title:  "SQL Server IDENTITY INSERT"
date:   2018-03-02 16:41:54 +0800
categories: SQL Server
published: true
author: ettingshausen
--- 


工作中，时不时会遇到导入数据的情况，对于自增列，SQL Server默认时不允许插入的。可以将`IDENTITY_INSERT` 设置为 `ON`。方法如下：

```sql
SET IDENTITY_INSERT [tableName] ON
``` 
这条命令时会话级别的。会话退出后，`IDENTITY_INSERT`会被置为`OFF`


>**注意**： 对于没有自增列的表，使用上述命令，会出错。表 'dbo.XXX' 没有标识属性。无法执行 SET 操作。  


在脚本中处理多张表的数据，这就需要判断表中是否有自增列。

```sql
SELECT OBJECTPROPERTY(OBJECT_ID('dbo.XXXX'),'TableHasIdentity')
```

如果表中包含自增列，则OBJECTPROPERTY(OBJECT_ID('dbo.XXXX'),'TableHasIdentity') 返回 `1`， 否则返回 `0`。 通过这个条件可以动态的设置表的`IDENTITY_INSERT`属性。

```sql
IF OBJECTPROPERTY(OBJECT_ID('dbo.XXX'),'TableHasIdentity')=1
BEGIN
    SET IDENTITY_INSERT dbo.XXX ON 
END
GO
``` 


# OBJECTPROPERTY函数[^objectproperty]
---



用法：
```sql
OBJECTPROPERTY ( id , property )
```
`id` 为表或者其他对象的唯一标识，`int`类型。  
`property` 为属性名称`varchar`类型，除`TableHasIdentity`外还有`TableHasForeignKey`等。
完整的列表参考MSDN官方文档[^objectproperty]
# 参考资料
----

[^objectproperty]: [OBJECTPROPERTY (Transact-SQL)](https://docs.microsoft.com/en-us/sql/t-sql/functions/objectproperty-transact-sql)


