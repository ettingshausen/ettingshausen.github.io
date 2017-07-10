---
layout: post
title:  "SQL Server 全角半角转换"
date:   2016-11-25 16:05:06 +0800
categories: SQL Serve
---

### 哪些字符是有全角和半角之分的？

首先，中文是只有全角，没有半角之分，所以转换的时候可以忽略掉中文字符。

+ **英文字符与标点**
对于英文字符来说，同字符半角的和全角的编码差值总是 `65248`。
```sql
select UNICODE('a'),UNICODE('ａ'),UNICODE('ａ')-UNICODE('a')
```

>|（无列名）|（无列名）|（无列名）|
|--- |---- |---- |
|97 | 265345 | 65248 |  

在[ASCII码](http://www.ascii-code.com/)中，英文字符与标点的编码范围为33~126，用SQL 通配符表示为：`[!-~]`，当然，全角字符的编码是超出ASCII编码范围的，只需在半角的ASCII的编码基础加上差值 `65248` 就能得到全角的[UNICODE](http://www.utf8-chartable.de/)编码。

+ **空格**
空格这个表特殊，全角为 `12288（0x3000）` ，半角为  `32（0x20）` 需要特殊处理下



--------------  

 了解了半角和全角的关系之后可以写个函数来相互转换

```sql
CREATE FUNCTION f_Convert(
  @str  NVARCHAR(4000), --要转换的字符串 
  @flag BIT         --转换标志,0转换成半角,1转换成全角 
)
  RETURNS NVARCHAR(4000)
AS
  BEGIN
    DECLARE
    @pat NVARCHAR(8),
    @step INT, 
    @i INT, 
    @spc INT
    
    IF @flag = 0
      SELECT
        @pat = N'%[！-～]%',    --全角的通配符
        @step = -65248,
        @str = REPLACE(@str, N'　 ', N'   ')
    ELSE
      SELECT
        @pat = N'%[!-~]%',      --半角的通配符
        @step = 65248,
        @str = REPLACE(@str, N'   ', N'　 ')
    --指定排序规则
    SET @i = PATINDEX(@pat collate LATIN1_GENERAL_BIN, @str)
    WHILE @i > 0
      SELECT
        @str = REPLACE(@str,
                       SUBSTRING(@str, @i, 1),
                       NCHAR(UNICODE(SUBSTRING(@str, @i, 1)) + @step)),
        @i = PATINDEX(@pat collate LATIN1_GENERAL_BIN, @str)
    RETURN (@str)
  END 
```