### Question
生产库里的那些数据类型...
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
### UniqueID & Snowflake
这是一种极其偷懒,且对未来系统压力未有预见性设计,单纯的数字估计只是出于使用自增列方式<BR>

一般业务推荐使用雪花算法(Snowflake)简单版本实现,即日期时间字段+序号组合
日期部分直接使用GETDATE函数获取系统时间
序号字段推荐序列方式实现[SEQUENCE](https://docs.microsoft.com/en-us/sql/t-sql/statements/create-sequence-transact-sql?view=sql-server-ver15)进行实现,SEQUENCE能较为容易重置初始位置
<BR>

```SQL
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
Working

### Question
你会使用SQL Server 什么数据类型存放折扣率 <BR>
A decimal<BR>
B float<BR>
C bigint<BR>
D 钝角<BR>
E smallmoney<BR>
F tinyint<BR>

 - D 作为保留选项是一定会存在的...<BR>
随后首先排除掉供应商同学选的C bigint, 我也不清楚及占用存储空间,又需要二次计算的选项是怎么考量的<BR>
一般理解思路为折扣是一个0.990000至0.10之间<BR>
即所谓99折或1折的中文语境表达方式<BR>

 - A decimal 以及 B float 可以纳入考虑范围<BR>
 - B float 是作为一个SQL Server 知识点<BR>

用于表示浮点数值数据的**大致**数值数据类型。 浮点数据为**近似值**；因此，**并非数据类型范围内的所有值都能精确地表示**。<BR>
**Approximate-number** data types for use with floating point numeric data. Floating point data is approximate; therefore, not all values in the data type range can be represented exactly.<BR>
https://docs.microsoft.com/zh-cn/sql/t-sql/data-types/float-and-real-transact-sql?
view=sql-server-ver16
<BR>

 - E smallmoney 可以两种解释方式
1. 存放折扣数字. 即作为存放已确定长度decimal,同decimal使用方式
2. 存放折扣后最终数值.个人推荐这种方式对于业务友好方式,例如一个价格为为85RMB的物品进行75折销售<BR>
    85*0.75 = 63.75<BR>
    一般业务会需要进行抹除角分,即63RMB,那么折扣率仅保留5位小数会存在误差,也无法进行统一处理<BR>
    63/75 = 0.741176470588235<BR>
    0.74117*85 = 62.99945<BR>

 - F tinyint 是一个值得玩味的答案,假如写成以下的形式也是一种值得肯定设计方式
<BR>

```SQL
IF OBJECT_ID('[dbo].[DiscountTable]', 'U') IS NOT NULL
DROP TABLE [dbo].[DiscountTable]
GO

CREATE TABLE DiscountTable  
   (  
    Discount tinyint
    CONSTRAINT CHK_Discount_Range   
    CHECK (Discount > 0 AND Discount < 100)
)
```
<BR>


