发现供应商提供的数据库中有很多类似列

<BR>

 ```SQL
With CET_Table(
COLUMN_NAME, 
DATA_TYPE, 
CHARACTER_MAXIMUM_LENGTH,
ROW_COUNT)
as
(
SELECT 

COLUMN_NAME, 
DATA_TYPE,
CHARACTER_MAXIMUM_LENGTH,
ROW_NUMBER() OVER(PARTITION BY COLUMN_NAME 
            ORDER BY [COLUMN_NAME] DESC)
FROM INFORMATION_SCHEMA.COLUMNS
WHERE COLUMN_NAME in ('created_date','created_by','create_by','create_date', 'createon','CreatedBy') 
OR COLUMN_NAME in('modified_date','ModifiedOn', 'ModifiedBy')
)
SELECT 
COLUMN_NAME, 
DATA_TYPE, 
CHARACTER_MAXIMUM_LENGTH
FROM CET_Table
WHERE ROW_COUNT <= 3
ORDER BY COLUMN_NAME ASC,ROW_COUNT ASC
-- 'modified_by','remark',
```
<BR>  


| COLUMN_NAME   | DATA_TYPE | CHARACTER_MAXIMUM_LENGTH |
| ------------- | --------- | ------------------------ |
| create_by     | varchar   | 50                       |
| create_by     | varchar   | 255                      |
| create_by     | nvarchar  | 255                      |
| create_date   | varchar   | 50                       |
| create_date   | datetime  | NULL                     |
| create_date   | varchar   | 50                       |
| created_by    | varchar   | 50                       |
| created_by    | varchar   | 50                       |
| created_by    | varchar   | 50                       |
| created_date  | varchar   | 30                       |
| created_date  | varchar   | 100                      |
| created_date  | varchar   | 20                       |
| createdBy     | varchar   | 50                       |
| createdBy     | varchar   | 100                      |
| createdBy     | varchar   | 100                      |
| modified_date | varchar   | 20                       |
| modified_date | varchar   | 20                       |
| modified_date | varchar   | 30                       |
| modifiedBy    | varchar   | 100                      |
| modifiedBy    | varchar   | 100                      |
| modifiedBy    | varchar   | 50                       |
| ModifiedOn    | varchar   | -1                       |
| ModifiedOn    | varchar   | -1                       |
| ModifiedOn    | varchar   | -1                       |
