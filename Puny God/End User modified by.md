### Question
发现供应商提供的数据库中有很多类似列<BR>

```SQL
-- Azure SQL Database 
With CET_Table(COLUMN_NAME, DATA_TYPE, CHARACTER_MAXIMUM_LENGTH,ROW_COUNT)
as
(
SELECT COLUMN_NAME, DATA_TYPE,CHARACTER_MAXIMUM_LENGTH,
ROW_NUMBER() OVER(PARTITION BY COLUMN_NAME 
            ORDER BY [COLUMN_NAME] DESC)
FROM INFORMATION_SCHEMA.COLUMNS
WHERE COLUMN_NAME in ('created_date','created_by','create_by','create_date', 'createon','CreatedBy') 
OR COLUMN_NAME in('modified_date','ModifiedOn', 'Modified_By')
)
SELECT 
COLUMN_NAME, DATA_TYPE,CHARACTER_MAXIMUM_LENGTH
FROM CET_Table
WHERE ROW_COUNT <= 3
ORDER BY COLUMN_NAME ASC,ROW_COUNT ASC
```
<BR>  

| COLUMN_NAME   | DATA_TYPE | CHARACTER_MAXIMUM_LENGTH |
| ------------- | --------- | ------------------------ |
| create_by     | varchar   | 50                       |
| create_by     | varchar   | 255                      |
| create_by     | nvarchar  | 255                      |
| create_date   | varchar   | 50                       |
| create_date   | varchar   | 100                      |
| create_date   | varchar   | 50                       |
| created_by    | nvarchar  | 64                       |
| created_by    | varchar   | 50                       |
| created_by    | varchar   | 50                       |
| created_date  | varchar   | 100                      |
| created_date  | varchar   | 30                       |
| created_date  | varchar   | 20                       |
| createdBy     | varchar   | 50                       |
| createdBy     | varchar   | 50                       |
| createdBy     | varchar   | 50                       |
| modified_by   | varchar   | 50                       |
| modified_by   | varchar   | 50                       |
| modified_by   | varchar   | 50                       |
| modified_date | varchar   | 20                       |
| modified_date | varchar   | 30                       |
| modified_date | nvarchar  | 64                       |
| ModifiedOn    | varchar   | -1                       |
| ModifiedOn    | varchar   | -1                       |
| ModifiedOn    | varchar   | -1                       |


<BR> 

内容基本定义为Created_By(何人创建),Create_Date(创建时间)<BR> 
Modified_By(何人修改),Modified_Date(何时修改)<BR>
数据一如既往的都使用varchar类型,这点就不再吐槽了,可以看到CHARACTER_MAXIMUM_LENGTH 还有值为-1，这并不是什么BUG,而是表示varchar(MAX)类型打(虽然我也不清楚为什么时间要放那么长)<BR>

个人认为完全不用将用户数据/业务数据与跟踪数据存放在同一张表中,当然再实现前有几点需要进行考虑<BR>
 - 业务表只存在Insert操作，业务存储查看历史更改状况,高频查询的为最终值(last Value)<BR>
 - 追踪信息查询高频为 Modified_By(何人修改),Modified_Date(何时修改)<BR>

 一些思考以及实现方式<BR>

 - 业务表需要修改时间(Modified_Date)列用于区分先后修改顺序,以及查询最新更新版本<BR>
 - 或使用[temporal table](https://docs.microsoft.com/en-us/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables?view=sql-server-ver15) 实现版本控制<BR>
 - Create_Date(创建时间) 仔细考虑其实并不需要单个列进行存放，而可以通过最早时间进行查询获得
 - Created_By(何人创建),Modified_By(何人修改)属于审计类型,应与业务数据分离存放

### Using Temporal Table
可以使用Temporal Table 方式进行的实现(不是很想写用张基础表Join事项方式)
 -  可以照常使用Update,Delete方式进行更新
 -  数据表默认存放为最新版本(Last Version)
 -  ValidFrom与ValidTo 为系统管理.时区为UTC时区,用户友好方式需要进行一层转换

<BR> 

#### CREATE Temporal Table
```SQL
-- Microsoft SQL Database 
CREATE TABLE dbo.T_Employee
(
  [EmployeeID] int NOT NULL PRIMARY KEY CLUSTERED
  , [Name] nvarchar(100) NOT NULL
  , [Position] varchar(100) NOT NULL
  , [ValidFrom] datetime2 GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )
WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.EmployeeHistory));

--制造一些数据
INSERT INTO [dbo].[T_Employee]([EmployeeID],[Name],[Position]) 
VALUES (1,'Isabella','C1'),
(2,'Ethan','C1'),
(3,'Amy','C1'),
(4,'Anthony','C1'),
(5,'Taj','C1'),
(6,'Hudson','C1'),
(7,'Jack','C1'),
(8,'Piper','C1');

WAITFOR DELAY '00:00:02:00';

UPDATE [dbo].[T_Employee]
SET [Position] = 'B1'
WHERE EmployeeID = '1'

WAITFOR DELAY '00:00:02:00';

UPDATE [dbo].[T_Employee]
SET [Position] = 'A1'
WHERE EmployeeID = '1'

WAITFOR DELAY '00:00:02:00';

DELETE [dbo].[T_Employee]
WHERE EmployeeID = '2'

```
<BR>  

#### Query Temporal Table
```SQL
-- Microsoft SQL Database 查询EmployeeID编号为1的历史记录 
SELECT * FROM dbo.[T_Employee]
FOR SYSTEM_TIME ALL
WHERE EmployeeID = '1'
ORDER BY ValidFrom DESC
```
<BR>  

| EmployeeID | Name     | Position | ValidFrom                   | ValidTo                     |
| ---------- | -------- | -------- | --------------------------- | --------------------------- |
| 1          | Isabella | A1       | 2022-05-21 10:58:16.5386151 | 9999-12-31 23:59:59.9999999 |
| 1          | Isabella | B1       | 2022-05-21 10:58:14.5309111 | 2022-05-21 10:58:16.5386151 |
| 1          | Isabella | C1       | 2022-05-21 10:58:12.5007888 | 2022-05-21 10:58:14.5309111 |

<BR> 
不使用WHERE子句整表查询结果相当数据仓库中的渐变维度表(Slowly Changing Dimension)
注意EmployeeID为2记录行中,虽然被删除但仍能查询获得

```SQL
-- Microsoft SQL Database 
SELECT * FROM dbo.[T_Employee]
FOR SYSTEM_TIME ALL
ORDER BY EmployeeID ASC,ValidFrom DESC
```
<BR>

| EmployeeID | Name     | Position | ValidFrom                   | ValidTo                     |
| ---------- | -------- | -------- | --------------------------- | --------------------------- |
| 1          | Isabella | A1       | 2022-05-21 10:58:16.5386151 | 9999-12-31 23:59:59.9999999 |
| 1          | Isabella | B1       | 2022-05-21 10:58:14.5309111 | 2022-05-21 10:58:16.5386151 |
| 1          | Isabella | C1       | 2022-05-21 10:58:12.5007888 | 2022-05-21 10:58:14.5309111 |
| 2          | Ethan    | C1       | 2022-05-21 10:58:12.5116103 | 2022-05-21 10:58:18.5452575 |
| 3          | Amy      | C1       | 2022-05-21 10:58:12.5116103 | 9999-12-31 23:59:59.9999999 |
| 4          | Anthony  | C1       | 2022-05-21 10:58:12.5116103 | 9999-12-31 23:59:59.9999999 |
| 5          | Taj      | C1       | 2022-05-21 10:58:12.5116103 | 9999-12-31 23:59:59.9999999 |
| 6          | Hudson   | C1       | 2022-05-21 10:58:12.5116103 | 9999-12-31 23:59:59.9999999 |
| 7          | Jack     | C1       | 2022-05-21 10:58:12.5116103 | 9999-12-31 23:59:59.9999999 |
| 8          | Piper    | C1       | 2022-05-21 10:58:12.5116103 | 9999-12-31 23:59:59.9999999 |

<BR> 

```SQL
-- Microsoft SQL Database 
```
<BR>  

#### DROP Temporal Table
不能直接使用DROP TABLE进行删除,需要关闭版本控制.随后删除所对应的两张表
也不能使用TRUNCATE TABLE
<BR> 

```SQL
-- Microsoft SQL Database 
ALTER TABLE [dbo].[T_Employee] SET ( SYSTEM_VERSIONING = OFF  )
GO

IF  EXISTS (SELECT * FROM sys.objects WHERE object_id = OBJECT_ID(N'[dbo].[T_Employee]') AND type in (N'U'))
DROP TABLE [dbo].[T_Employee]
GO

IF  EXISTS (SELECT * FROM sys.objects WHERE object_id = OBJECT_ID(N'[dbo].[EmployeeHistory]') AND type in (N'U'))
DROP TABLE [dbo].[EmployeeHistory]
GO
```

<BR>  

由于默认使用UTC时区,为了大家的便利性创建视图, 并降低一定的时间精度(毫秒对于一般业务意义不大)

```SQL
-- Microsoft SQL Database 
IF  EXISTS (SELECT * FROM sys.objects WHERE object_id = OBJECT_ID(N'[dbo].[V_Employee_ChinaTimeZone]') AND type in (N'V'))
DROP VIEW V_Employee_ChinaTimeZone
--创建中国时区视图
GO
CREATE VIEW V_Employee_ChinaTimeZone
AS
SELECT [EmployeeID]
      ,[Name]
      ,[Position]
      ,CAST([ValidFrom] AT TIME ZONE 'UTC' AT TIME ZONE 'China Standard Time' as smalldatetime) AS LastUpdate
  FROM [Mjolnir].[dbo].[T_Employee]
GO
IF EXISTS (SELECT * FROM sys.objects WHERE object_id = OBJECT_ID(N'[Employee]') AND type in (N'SN'))
DROP SYNONYM [Employee]
--创建对应同义词
CREATE SYNONYM Employee  
FOR V_Employee_ChinaTimeZone;  
--这样取得的是版本最新值(Last Version)
SELECT * FROM Employee
```
<BR>

| EmployeeID | Name     | Position | LastUpdate          |
| ---------- | -------- | -------- | ------------------- |
| 1          | Isabella | A1       | 2022-05-21 19:32:00 |
| 3          | Amy      | C1       | 2022-05-21 19:32:00 |
| 4          | Anthony  | C1       | 2022-05-21 19:32:00 |
| 5          | Taj      | C1       | 2022-05-21 19:32:00 |
| 6          | Hudson   | C1       | 2022-05-21 19:32:00 |
| 7          | Jack     | C1       | 2022-05-21 19:32:00 |
| 8          | Piper    | C1       | 2022-05-21 19:32:00 |

<BR>可以发现其创建日期(就是第一次更新日期), 完全没有必要去占用一列去存储

### Build Store Produce
 - DELETE 操作可以单独创建SP_DELETE_Employee<BR>
 - INSERT与UPDATE 在并发要求不高情况下可以考虑MERGE 编写<BR>

<BR> 

#### Store Produce DELETE PART(Parameterization) 
```SQL
-- DELETE PART SQL 写把变量写好测试一下
DECLARE @EmployeeIDvar int;  
SET @EmployeeIDvar = 3
--SELECT @EmployeeIDvar

DELETE [dbo].[T_Employee]
WHERE EmployeeID = @EmployeeIDvar

-- DELETE PART Store Produce 只接受一个参数,参数就是EmployeeID
IF  EXISTS (SELECT * FROM sys.objects WHERE object_id = OBJECT_ID(N'[dbo].[SP_DELETE_Employee]') AND type in (N'P'))
DROP PROCEDURE [dbo].[SP_DELETE_Employee]

CREATE PROCEDURE dbo.SP_DELETE_Employee   
    @EmployeeIDvar int 
AS   
    SET NOCOUNT ON;
    DELETE [dbo].[T_Employee]
WHERE EmployeeID = @EmployeeIDvar

EXECUTE SP_Merge_Employee @EmployeeIDvar = 3

SELECT * FROM Employee

```
<BR>

#### Store Produce Table Value Constructor PART
```SQL
CREATE TYPE Employee_Recode_Type AS TABLE
(
    [EmployeeID] int,
    [Name] VARCHAR(200),
    [Position] VARCHAR(100)
)
DECLARE @Employee_Recode AS Employee_Recode_Type
INSERT INTO @Employee_Recode
VALUES(15,'Ling','C1'),(14,'Ling','C1') --可以直接传入多行
SELECT * FROM Employee_Recode
```
<BR>

| EmployeeID | Name | Position |
| ---------- | ---- | -------- |
| 15         | Ling | C1       |
| 14         | Ling | C1       |

<BR>

#### Store Produce MERGE PART
```SQL
CREATE OR ALTER PROCEDURE dbo.SP_MERGE_Employee
@Employee_Recode Employee_Recode_Type
AS
SET NOCOUNT ON
MERGE INTO T_Employee AS TGT --目标表
USING  @Employee_Recode AS SRC 
ON SRC.EmployeeID = TGT.EmployeeID  -- 可以理解为Join

WHEN MATCHED AND TGT.EmployeeID = TGT.EmployeeID  --可以理解为达成条件
THEN
UPDATE SET [Position] = SRC.Position --无法添加Where子句

WHEN NOT MATCHED BY TARGET
THEN 
INSERT ([EmployeeID],[Name],[Position]) 
VALUES (SRC.EmployeeID,SRC.Name,SRC.Position)
```
#### Store Produce EXEC PART
```SQL
DECLARE @Employee_Recode AS Employee_Recode_Type;
INSERT INTO @Employee_Recode
VALUES(15,'Ling','C1'),(14,'Ling','C1') --可以直接传入多行

EXEC dbo.SP_MERGE_Employee @Employee_Recode

```
