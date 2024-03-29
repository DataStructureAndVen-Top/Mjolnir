### Question
一个假想的问题
假如一个列名称叫做create_date 请问这个列大概是什么类型<BR>
A date <BR>
B datetime<BR> 
C varchar(50）<BR>
D 钝角  <BR>
供应商同学竟然没有选 D 钝角 而是选择C varchar(50)<BR>
结果自然是啥都能往里放，作为数据仓库数据类型不一致会导致报表制作环境相当烦躁，后续供应商也需要进行判断以及进行各种类型转换
<BR>

### In Database
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
 <BR>  

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

### Date Type(Datetime&Date)

首先大多RDBMS 都会提供DATETIME以及DATE类型,基本都考虑了大部分时间存放一些维度（日期部分（YYYY-MM-DD）时间部分含毫秒部分（hh:mm:ss[.nnn]）,时区部分([+|-]hh:mm)，）<BR>
对于数据仓库系统(Data Warehouse)来说一般会存在时间维度表(日期表),存放一些企业特定财年(Fiscal Year) 对应于自然年(Civil Year),以及一些星期(Week)定义<BR>
一般可以去抄微软的作业<BR>

```SQL
-- Microsoft SQL Database 
SELECT TOP(10) *
FROM AdventureWorksDW2016_EXT.dbo.DimDate;
```
<BR>

| DateKey  | FullDateAlternateKey | DayNumberOfWeek | EnglishDayNameOfWeek | SpanishDayNameOfWeek | FrenchDayNameOfWeek | DayNumberOfMonth | DayNumberOfYear | WeekNumberOfYear | EnglishMonthName | SpanishMonthName | FrenchMonthName | MonthNumberOfYear | CalendarQuarter | CalendarYear | CalendarSemester | FiscalQuarter | FiscalYear | FiscalSemester |
| -------- | -------------------- | --------------- | -------------------- | -------------------- | ------------------- | ---------------- | --------------- | ---------------- | ---------------- | ---------------- | --------------- | ----------------- | --------------- | ------------ | ---------------- | ------------- | ---------- | -------------- |
| 20050101 | 2005-01-01           | 7               | Saturday             | Sábado               | Samedi              | 1                | 1               | 1                | January          | Enero            | Janvier         | 1                 | 1               | 2005         | 1                | 3             | 2005       | 2              |
| 20050102 | 2005-01-02           | 1               | Sunday               | Domingo              | Dimanche            | 2                | 2               | 2                | January          | Enero            | Janvier         | 1                 | 1               | 2005         | 1                | 3             | 2005       | 2              |
| 20050103 | 2005-01-03           | 2               | Monday               | Lunes                | Lundi               | 3                | 3               | 2                | January          | Enero            | Janvier         | 1                 | 1               | 2005         | 1                | 3             | 2005       | 2              |
| 20050104 | 2005-01-04           | 3               | Tuesday              | Martes               | Mardi               | 4                | 4               | 2                | January          | Enero            | Janvier         | 1                 | 1               | 2005         | 1                | 3             | 2005       | 2              |
| 20050105 | 2005-01-05           | 4               | Wednesday            | Miércoles            | Mercredi            | 5                | 5               | 2                | January          | Enero            | Janvier         | 1                 | 1               | 2005         | 1                | 3             | 2005       | 2              |
| 20050106 | 2005-01-06           | 5               | Thursday             | Jueves               | Jeudi               | 6                | 6               | 2                | January          | Enero            | Janvier         | 1                 | 1               | 2005         | 1                | 3             | 2005       | 2              |
| 20050107 | 2005-01-07           | 6               | Friday               | Viernes              | Vendredi            | 7                | 7               | 2                | January          | Enero            | Janvier         | 1                 | 1               | 2005         | 1                | 3             | 2005       | 2              |
| 20050108 | 2005-01-08           | 7               | Saturday             | Sábado               | Samedi              | 8                | 8               | 2                | January          | Enero            | Janvier         | 1                 | 1               | 2005         | 1                | 3             | 2005       | 2              |
| 20050109 | 2005-01-09           | 1               | Sunday               | Domingo              | Dimanche            | 9                | 9               | 3                | January          | Enero            | Janvier         | 1                 | 1               | 2005         | 1                | 3             | 2005       | 2              |
| 20050110 | 2005-01-10           | 2               | Monday               | Lunes                | Lundi               | 10               | 10              | 3                | January          | Enero            | Janvier         | 1                 | 1               | 2005         | 1                | 3             | 2005       | 2              |

<BR>

```SQL
-- Microsoft SQL Database 
SELECT TOP(10)* 
FROM WideWorldImportersDW.Dimension.Date;
```
<BR>

| Date       | Day Number | Day | Month   | Short Month | Calendar Month Number | Calendar Month Label | Calendar Year | Calendar Year Label | Fiscal Month Number | Fiscal Month Label | Fiscal Year | Fiscal Year Label | ISO Week Number |
| ---------- | ---------- | --- | ------- | ----------- | --------------------- | -------------------- | ------------- | ------------------- | ------------------- | ------------------ | ----------- | ----------------- | --------------- |
| 2013-01-01 | 1          | 1   | January | Jan         | 1                     | CY2013-Jan           | 2013          | CY2013              | 3                   | FY2013-Jan         | 2013        | FY2013            | 1               |
| 2013-01-02 | 2          | 2   | January | Jan         | 1                     | CY2013-Jan           | 2013          | CY2013              | 3                   | FY2013-Jan         | 2013        | FY2013            | 1               |
| 2013-01-03 | 3          | 3   | January | Jan         | 1                     | CY2013-Jan           | 2013          | CY2013              | 3                   | FY2013-Jan         | 2013        | FY2013            | 1               |
| 2013-01-04 | 4          | 4   | January | Jan         | 1                     | CY2013-Jan           | 2013          | CY2013              | 3                   | FY2013-Jan         | 2013        | FY2013            | 1               |
| 2013-01-05 | 5          | 5   | January | Jan         | 1                     | CY2013-Jan           | 2013          | CY2013              | 3                   | FY2013-Jan         | 2013        | FY2013            | 1               |
| 2013-01-06 | 6          | 6   | January | Jan         | 1                     | CY2013-Jan           | 2013          | CY2013              | 3                   | FY2013-Jan         | 2013        | FY2013            | 1               |
| 2013-01-07 | 7          | 7   | January | Jan         | 1                     | CY2013-Jan           | 2013          | CY2013              | 3                   | FY2013-Jan         | 2013        | FY2013            | 2               |
| 2013-01-08 | 8          | 8   | January | Jan         | 1                     | CY2013-Jan           | 2013          | CY2013              | 3                   | FY2013-Jan         | 2013        | FY2013            | 2               |
| 2013-01-09 | 9          | 9   | January | Jan         | 1                     | CY2013-Jan           | 2013          | CY2013              | 3                   | FY2013-Jan         | 2013        | FY2013            | 2               |
| 2013-01-10 | 10         | 10  | January | Jan         | 1                     | CY2013-Jan           | 2013          | CY2013              | 3                   | FY2013-Jan         | 2013        | FY2013            | 2               |
<BR>

可以看到微软两者实现方式也所不同,<BR>
个人其实更加倾向于WideWorldImportersDW方式<BR>
 - Schema部分有所意义(AdventureWorksDW2016_EXT Schema均为dbo.),但就无法使用[sp_help](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-help-transact-sql?view=sql-server-ver15#arguments)<BR>
 - Calendar Month Label,Calendar Year Label,Fiscal Month Label,Fiscal Year Label 均使用文本方式存放,
    带有前缀CY,FY避免之后的误解,当然对于某些用户更习惯于YYYYMM显示方式,需要在个位数月份前填充0<BR>
    为了节约空间Calendar Year,Calendar Month Number，Fiscal Month Number,Fiscal Year，ISO Week Number 使用tinyint(0到255范围，仅占1个字节)

```SQL
-- Microsoft SQL Database 
USE WideWorldImportersDW
SELECT
    [TABLE_NAME],
    [COLUMN_NAME],
    [DATA_TYPE],
    [CHARACTER_MAXIMUM_LENGTH]
FROM INFORMATION_SCHEMA.Columns
WHERE TABLE_NAME = 'Date'
ORDER BY [DATA_TYPE]
```
<BR>

| TABLE_NAME | COLUMN_NAME           | DATA_TYPE | CHARACTER_MAXIMUM_LENGTH |
| ---------- | --------------------- | --------- | ------------------------ |
| Date       | Date                  | date      | NULL                     |
| Date       | Day Number            | int       | NULL                     |
| Date       | Calendar Month Number | int       | NULL                     |
| Date       | Calendar Year         | int       | NULL                     |
| Date       | Fiscal Month Number   | int       | NULL                     |
| Date       | Fiscal Year           | int       | NULL                     |
| Date       | ISO Week Number       | int       | NULL                     |
| Date       | Fiscal Year Label     | nvarchar  | 10                       |
| Date       | Fiscal Month Label    | nvarchar  | 20                       |
| Date       | Calendar Year Label   | nvarchar  | 10                       |
| Date       | Calendar Month Label  | nvarchar  | 20                       |
| Date       | Day                   | nvarchar  | 10                       |
| Date       | Month                 | nvarchar  | 10                       |
| Date       | Short Month           | nvarchar  | 3                        |

<BR>

### Reference
>Oracle Date Type<br>
>https://docs.oracle.com/cd/B19306_01/olap.102/b14346/dml_datatypes005.htm<br>
>SQL Server Date Type<br>
>https://docs.microsoft.com/zh-cn/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=sql-server-ver15<br>
>SQL Server Time Type<br>
>https://docs.microsoft.com/zh-cn/sql/t-sql/data-types/time-transact-sql?view=sql-server-ver15<br>
>SQL Server smalldatetime Type<br>
>https://docs.microsoft.com/zh-cn/sql/t-sql/data-types/smalldatetime-transact-sql?view=sql-server-ver15<br>
>MySQL Year Type<br>
>https://dev.mysql.com/doc/refman/5.7/en/year.html<br>

