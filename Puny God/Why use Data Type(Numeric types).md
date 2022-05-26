### Question
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
### Question UniqueID & Snowflake
这是一种极其偷懒,且对未来系统压力未有预见性设计,单纯的数字估计只是出于使用自增列方式<BR>

一般业务推荐使用雪花算法(Snowflake)简单版本实现,即日期时间字段+序号组合
日期部分直接使用GETDATE函数获取系统时间
序号字段推荐序列方式实现[SEQUENCE](https://docs.microsoft.com/en-us/sql/t-sql/statements/create-sequence-transact-sql?view=sql-server-ver15)进行实现,SEQUENCE能较为容易重置初始位置
<BR>




IF OBJECT_ID('[dbo].[Snowflake]', 'U') IS NOT NULL
DROP TABLE [dbo].[Snowflake]
GO
-- Create the table in the specified schema
CREATE TABLE [dbo].[Snowflake]
(
  
    [Snowflake_Date] datetime2  NOT NULL DEFAULT GETDATE(),
    [Snowflake_ID] INT NOT NULL  --IDENTITY(int, 1, 1),
    CONSTRAINT PK_Snowflake_ID PRIMARY KEY CLUSTERED (Snowflake_Date,Snowflake_ID),
    [Name] nvarchar(100) NOT NULL
);



INSERT INTO [dbo].[Snowflake](Name) 
VALUES ('Isabella'),('Ethan'),('Amy'),('Anthony'),('Taj'),('Hudson'),('Jack'),('Piper')
WAITFOR DELAY '00:00:02:00';
INSERT INTO [dbo].[Snowflake](Name) 
VALUES ('Isabella'),('Ethan'),('Amy'),('Anthony'),('Taj'),('Hudson'),('Jack'),('Piper')
```

### Build Store Produce

需要进行SP 分装





 - float