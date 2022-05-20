发现供应商提供的数据库中有很多类似列

<BR>

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
 - Created_By(何人创建),Create_Date(创建时间) 仔细考虑其实并不需要单个列进行存放，而可以通过最早时间进行查询获得

Temporal Table 实现方式
 -  可以照常使用Update,Delete方式进行更新
 -  数据表默认存放为最新版本(Last Version)值

表结构实现方式
 -  只能使用Insert方式提交修改值,如需要删除值需要特别标记
 -  需要自定义的时间列用于时间戳
 -  需要使用条件获得最新版本(Last Version)值
 -  