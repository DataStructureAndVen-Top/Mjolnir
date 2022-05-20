
<BR>

```SQL
-- Azure SQL Database 
SELECT DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
GROUP BY DATA_TYPE
```
<BR>  

| DATA_TYPE |
| --------- |
| bigint    |
| bit       |
| char      |
| date      |
| datetime  |
| datetime2 |
| decimal   |
| float     |
| int       |
| ntext     |
| numeric   |
| nvarchar  |
| smallint  |
| text      |
| timestamp |
| tinyint   |
| varbinary |
| varchar   |

<BR> 


进一步查询,可以发现大多的bigint类型作为ID进行使用<BR>
这是一种极其偷懒,且对未来系统压力未有预见性设计,单纯的数字估计只是出于使用[SEQUENCE](https://docs.microsoft.com/en-us/sql/t-sql/statements/create-sequence-transact-sql?view=sql-server-ver15)的便利,推荐在单表序列环境下使用[IDENTITY](https://docs.microsoft.com/en-us/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15)<BR>

一般业务可以实现一个雪花算法(Snowflake)简单版本,即日期时间字段+序号组合

```SQL
-- Azure SQL MI 
IF OBJECT_ID('[dbo].[Snowflake]', 'U') IS NOT NULL
DROP TABLE [dbo].[Snowflake]
GO
-- Create the table in the specified schema
CREATE TABLE [dbo].[Snowflake]
(
  
    [Snowflake_Date] smalldatetime  NOT NULL, -- 最大值 2079-06-06 分钟精度
    [Snowflake_ID] INT NOT NULL  IDENTITY(1,2147483640),
    CONSTRAINT PK_Snowflake_ID PRIMARY KEY CLUSTERED (Snowflake_Date,Snowflake_ID)
);
GO

```



<BR>

```SQL
-- Azure SQL Database 
SELECT DATA_TYPE,COUNT(DATA_TYPE) AS Count
FROM INFORMATION_SCHEMA.COLUMNS
WHERE DATA_TYPE = 'bigint' 
AND COLUMN_NAME LIKE '%ID'
GROUP BY DATA_TYPE
```
<BR>  




 - float