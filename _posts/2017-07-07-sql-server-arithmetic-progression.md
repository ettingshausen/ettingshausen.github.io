---
layout: post
title:  "SQL Server 等差数列求和"
date:   2017-07-07 20:23:06 +0800
categories: SQL Server
---

1+2+3 +...+100这种问题，首相想到的是用循环解决。代码如下
```sql
;declare @v_i int = 1,@v_sum int = 0
while @v_i <=  100
	begin
		set @v_sum = @v_sum + @v_i
		set @v_i = @v_i + 1
	end
print @v_sum
```

最近发现可以用`with as`特性同样可以解决

```sql
;with a(v) as(
	select 1 
	union all
	select v+1 from a where v+1<=100)
select sum(v) from a
```