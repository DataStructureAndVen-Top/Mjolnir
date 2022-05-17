
问个问题
 假如一个列名称叫做create_date 请问这个列大概是什么类型<br>
 A date <br>
 B datetime<br> 
 C varchar(50）<br>
 D 钝角  <br>

首先大多RDBMS 都会提供DATETIME以及DATE类型<br>
Oracle Data Type<br>
https://docs.oracle.com/database/121/SQLRF/sql_elements001.htm#SQLRF0021<br>

SQL Server Data Type<br>
https://docs.microsoft.com/zh-cn/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=sql-server-ver15<br>

部分为了节约存储空间会提供类似<br>

SQL Server Time Type<br>  
https://docs.microsoft.com/zh-cn/sql/t-sql/data-types/time-transact-sql?view=sql-server-ver15<br>
SQL Server smalldatetime Type<br>  
https://docs.microsoft.com/zh-cn/sql/t-sql/data-types/smalldatetime-transact-sql?view=sql-server-ver15<br>
MySQL Year Type<br>
https://dev.mysql.com/doc/refman/5.7/en/year.html<br>

当然这些类型可以系统时间函数进行获得，当然在业务会存在类似与年月（YYYYMM）的数据类型。一天推荐使用char(8)来存放,前缀FY,CY用于区分自然年以及财务年<br>



当然在数据仓库环境一下一般会存在时间维度表
=======

```SQL
SP_Help 'Dimension.[Date]'
```


```SQL
SELECT *
FROM Dimension.[Date]
```

