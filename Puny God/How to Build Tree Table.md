关系型数据库并不直接树状结构(Tree Table),以下集中方式可以进行选择


#### 父子结构
这种方式对于查询同层查询较为便利（广度查询）,但对于树状结构查询较为困难（深度查询）
单表使用外键方式实现
```SQL

IF OBJECT_ID('[dbo].[Employee]', 'U') IS NOT NULL
DROP TABLE [dbo].[Employee]
GO

CREATE TABLE Employee  
   (  
    BusinessEntityID int
    CONSTRAINT PK_BusinessEntityID  PRIMARY KEY NONCLUSTERED (BusinessEntityID),
    ManagerID int 
    CONSTRAINT FK_BusinessEntityID REFERENCES Employee(BusinessEntityID),
    EmployeeName nvarchar(50)   
   );  
INSERT INTO [dbo].[Employee](BusinessEntityID,ManagerID,EmployeeName) 
VALUES (1,NULL,'Isabella'),(2,1,'Ethan'),(3,1,'Amy'),(4,2,'Anthony'),(5,1,'Taj'),(6,4,'Hudson'),(7,6,'Jack'),(8,7,'Piper')

SELECT * FROM Employee
```
<BR>

| BusinessEntityID | ManagerID | EmployeeName |
| ---------------- | --------- | ------------ |
| 1                | NULL      | Isabella     |
| 2                | 1         | Ethan        |
| 3                | 1         | Amy          |
| 4                | 2         | Anthony      |
| 5                | 1         | Taj          |
| 6                | 4         | Hudson       |
| 7                | 6         | Jack         |
| 8                | 7         | Piper        |

<BR>
查询平层的同事
<BR>

```SQL

DECLARE @varManagerID INT
SELECT @varManagerID  =  ManagerID 
FROM Employee WHERE EmployeeName = 'Piper'
SELECT * FROM Employee
WHERE ManagerID = @varManagerID

```
| BusinessEntityID | ManagerID | EmployeeName |
| ---------------- | --------- | ------------ |
| 7                | 6         | Jack         |
| 8                | 6         | Piper        |

假如需要知道上层老板
<BR>

```SQL
DECLARE @varManagerID INT
SELECT @varManagerID  =  ManagerID 
FROM Employee WHERE EmployeeName = 'Piper'


SELECT 
Manager.BusinessEntityID as ManagerID,
Colleague.BusinessEntityID as ColleagueID,
Manager.EmployeeName as ManagerName,
Colleague.EmployeeName as ColleagueName
FROM Employee AS Manager
INNER JOIN  Employee AS Colleague
ON Manager.BusinessEntityID = Colleague.ManagerID
WHERE Colleague.ManagerID = @varManagerID
```
<BR>
| ManagerID | ColleagueID | ManagerName | ColleagueName |
| --------- | ----------- | ----------- | ------------- |
| 6         | 7           | Hudson      | Jack          |
| 6         | 8           | Hudson      | Piper         |
<BR>

```SQL

With CET_Manager_1(ManagerID,Manager_1,Colleague)
as
(
SELECT 
Manager_1.ManagerID as ManagerID ,
Manager_1.EmployeeName as Manager_1,
Colleague.EmployeeName as Colleague
FROM Employee AS Manager_1
RIGHT JOIN  Employee AS Colleague
ON Manager_1.BusinessEntityID = Colleague.ManagerID 
WHERE Colleague.ManagerID = 
    (
    SELECT ManagerID 
    FROM Employee WHERE EmployeeName = 'Piper' --查询同事
    -- FROM Employee WHERE EmployeeName = 'Anthony' --查询同事

    )
),
-- SELECT * FROM CET_Manager_1
CET_Manager_2(ManagerID,Manager_2,Manager_1,Colleague)
as
(
SELECT 
Manager_2.ManagerID as ManagerID,
Manager_2.EmployeeName as Manager_2,
Manager_1.Manager_1 as Manager_1,
Manager_1.Colleague as Colleague
FROM Employee AS Manager_2
RIGHT JOIN  CET_Manager_1 AS Manager_1
ON Manager_1.ManagerID = Manager_2.BusinessEntityID
),
--  select * from CET_Manager_2
CET_Manager_3(ManagerID,Manager_3,Manager_2,Manager_1,Colleague)
as
( 
SELECT 
-- Manager_2.ManagerID,
Manager_3.ManagerId as ManagerID,
Manager_3.EmployeeName as Manager_3,
Manager_2.Manager_2 as Manager_2,
Manager_2.Manager_1 as Manager_1,
Manager_2.Colleague as Colleague
FROM Employee AS Manager_3
RIGHT JOIN  CET_Manager_2 AS Manager_2
ON Manager_2.ManagerID = Manager_3.BusinessEntityID
),
CET_Manager_4(Manager_4,Manager_3,Manager_2,Manager_1,Colleague)
as
( 
SELECT 
-- Manager_2.ManagerID,
-- Manager_4.ManagerId as ManagerID,
Manager_4.EmployeeName as Manager_4,
Manager_3.Manager_3 as Manager_3,
Manager_3.Manager_2 as Manager_3,
Manager_3.Manager_1 as Manager_1,
Manager_3.Colleague as Colleague
FROM Employee AS Manager_4
RIGHT JOIN  CET_Manager_3 AS Manager_3
ON Manager_3.ManagerID = Manager_4.BusinessEntityID
)
SELECT * FROM CET_Manager_4
```

| Manager_4 | Manager_3 | Manager_2 | Manager_1 | Colleague |
| --------- | --------- | --------- | --------- | --------- |
| Isabella  | Ethan     | Anthony   | Hudson    | Jack      |
| Isabella  | Ethan     | Anthony   | Hudson    | Piper     |
<BR>
| Manager_4 | Manager_3 | Manager_2 | Manager_1 | Colleague |
| --------- | --------- | --------- | --------- | --------- |
| NULL      | NULL      | Isabella  | Ethan     | Anthony   |
<BR>

但需要树状结构表示就会较为复杂,每一层需要Join操作进行<BR>
且无法使用通用方式进行查询(无法预先所致树层次数)<BR>
也无法有一个员工有多为主管方式(变相实现方式为每次主管设计一个组)<BR>

#### 层次结构
 -  [Hierarchyid](https://docs.microsoft.com/en-us/sql/relational-databases/hierarchical-data-sql-server?view=sql-server-ver15)<BR>
假如需要确保为树状结构,可以使用[强制使用树状结构](https://docs.microsoft.com/en-us/sql/relational-databases/hierarchical-data-sql-server?view=sql-server-ver15#BKMK_EnforcingTrees)
与父子结构相同.官方实现较为全面就不写Code,直接参考即可


#### SQL Graph 结构
 -  [SQL Graph](https://docs.microsoft.com/zh-cn/sql/relational-databases/graphs/sql-graph-architecture?view=sql-server-ver15)<BR>








 -  Semi-Structured
    -  [XML](https://docs.microsoft.com/en-us/sql/relational-databases/xml/xml-data-sql-server?view=sql-server-ver15)
    -  [JSON](https://docs.microsoft.com/zh-cn/sql/relational-databases/json/json-data-sql-server?view=sql-server-ver15)


1. 邮件推送功能不完善，没有定时定时邮件推送，无法按需快速定制邮件提醒	
   门户默认提供，发送报表依赖于数仓数据提供
2. 无法导出PDF，希望可以实现导出所展示的报表
   门户默认提供
3. 无法手动按需刷新 - 希望支持用户自行刷新
   页面需要插件辅助，数据实时性需要更改数据链路
4. 交互界面不够友好，无法快速定制
   体验问题具体分析
5. 动态权限控制，如动态跟随org chart进行控制
   需要对应权限表
6. Report Server无法提供API接口，只能通过Iframe整体嵌入其他Application
   我没理解的问题，需要实现何种功能
7. 没有报表搜索功能，缺少报表tag(权限控制)
   需要对应标签表
8. 无法定义报表self-explanation功能，用户体验不够好
   体验问题具体分析
9.  缺少有效的报表异常数据或优化建议反馈途径
   这是流程问题吧
10. 报表统计数据不够精细，可用的维度不够
   数仓性质问题
11. 线上数据录入，提供数据录入的途径（权限控制）
   MDM系统问题

