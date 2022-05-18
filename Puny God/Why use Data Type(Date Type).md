
问个问题
 假如一个列名称叫做create_date 请问这个列大概是什么类型<br>
 A date <br>
 B datetime<br> 
 C varchar(50）<br>
 D 钝角  <br>
 供应商同学竟然没有选 D 钝角 而是选择C varchar(50),使得内容校验极其麻烦
 
 @import "https://github.com/DataStructureAndVen-Top/Mjolnir/blob/main/Puny%20God/Timestamp_Row3.csv"


```SQL
-- Azure SQL Database 
,CET_Table_Timestamp (timestamp,Length,RowNumber)
as 
(
SELECT 
timestamp, len(timestamp) as Length,
ROW_NUMBER() OVER(PARTITION BY len(timestamp) ORDER BY [timestamp] DESC) AS RowNumber
FROM CET_Table
group by timestamp
)
SELECT *
FROM CET_Table_Timestamp
WHERE RowNumber <=3
```




首先大多RDBMS 都会提供DATETIME以及DATE类型<br>
Oracle Date Type<br>
https://docs.oracle.com/cd/B19306_01/olap.102/b14346/dml_datatypes005.htm<br>

SQL Server Date Type<br>
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

