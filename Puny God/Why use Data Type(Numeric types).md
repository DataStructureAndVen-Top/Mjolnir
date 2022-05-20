
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
这是一种极其偷懒,且对未来系统压力未有预见性设计<BR>


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