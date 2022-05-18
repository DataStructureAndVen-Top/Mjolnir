
问个问题
 假如一个列名称叫做create_date 请问这个列大概是什么类型<br>
 A date <br>
 B datetime<br> 
 C varchar(50）<br>
 D 钝角  <br>
 供应商同学竟然没有选 D 钝角 而是选择C varchar(50)<br>
 结果自然是啥都能往里放，作为数据仓库数据类型不一致会导致报表制作环境相当烦躁，后续供应商也需要进行判断以及进行各种类型转换<br>  
| timestamp                     | Length | RowNumber |
| ----------------------------- | ------ | --------- |
| NULL                          | NULL   | 1         |
| 2022-05-18 01:30:09           | 19     | 1         |
| 2022-05-18 00:30:00           | 19     | 2         |
| 2022-05-18 00:20:00           | 19     | 3         |
| 2022-05-16T09:18:00.230       | 23     | 1         |
| 2022-05-15T18:50:27.723       | 23     | 2         |
| 2022-05-15T18:32:51.043       | 23     | 3         |
| 2022-05-10T13:32:41.626Z      | 24     | 1         |
| 2022-05-10T13:32:41.605Z      | 24     | 2         |
| 2022-05-10T13:30:38.925Z      | 24     | 3         |
| 2022-05-17T23:58:28.345+08:00 | 29     | 1         |
| 2022-05-17T23:58:05.132+08:00 | 29     | 2         |
| 2022-05-17T23:30:06.206+08:00 | 29     | 3         |
<BR>

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
<br>

首先大多RDBMS 都会提供DATETIME以及DATE类型,基本都考虑了大部分时间存放一些维度（日期部分（YYYY-MM-DD）时间部分含毫秒部分（hh:mm:ss[.nnn]）,时区部分([+|-]hh:mm)，）<br>

对于数据仓库系统(Data Warehouse)来说一般会存在时间维度表
去存放一些企业特定的财年(Fiscal Year) 对应于自然年(Civil Year),以及一些特殊的周(Week)定义



当然在数据仓库环境一下一般会存在时间维度表
=======

```SQL
SP_Help 'Dimension.[Date]'
```


```SQL
SELECT *
FROM Dimension.[Date]
```




>Oracle Date Type<br>
>https://docs.oracle.com/cd/B19306_01/olap.102/b14346/dml_datatypes005.htm<br>

>SQL Server Date Type<br>
>https://docs.microsoft.com/zh-cn/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=sql-server-ver15<br>

>部分为了节约存储空间会提供类似<br>
>SQL Server Time Type<br>
>https://docs.microsoft.com/zh-cn/sql/t-sql/data-types/time-transact-sql?view=sql-server-ver15<br>
>SQL Server smalldatetime Type<br>
>https://docs.microsoft.com/zh-cn/sql/t-sql/data-types/smalldatetime-transact-sql?view=sql-server-ver15<br>
>MySQL Year Type<br>
>https://dev.mysql.com/doc/refman/5.7/en/year.html<br>

