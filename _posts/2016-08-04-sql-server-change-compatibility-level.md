---
layout: post
title:  "SQL Server修改数据库兼容性级别"
date:   2016-08-04 10:05:06 +0800
categories: SQL Server
---


在SQL Server中使用Merge into需要数据库在2008版本以上才行。如果库是从之前的版本还原的，则兼容性级别可能还停留在之前的版本。

在库的属性中可以修改兼容性级别，如下图所示。

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1335634-fb56c04629e9f197.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


也可以通过脚本执行完成兼容性的更改，脚本如下：

```sql

declare

@v_compatibility_level int,
@v_database_name varchar(20),
@v_sql_command varchar(200)

begin

select  @v_database_name = DB_NAME() ;
print @v_database_name;
SELECT @v_compatibility_level=compatibility_level  
FROM sys.databases WITH(NOLOCK)
where name = @v_database_name

print @v_compatibility_level
set @v_sql_command= 'ALTER DATABASE '+ @v_database_name + ' SET COMPATIBILITY_LEVEL = 100'
print @v_sql_command
if @v_compatibility_level <= 100
exec (@v_sql_command)

end
```