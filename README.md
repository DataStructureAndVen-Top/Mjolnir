# Overview 
  没用使用Github Pages,对于阅读用户足够友好,还是做个大纲进行导读
  环境配置可以参考[这里](https://github.com/DataStructureAndVen-Top/Mjolnir#preparations-and-necessary-software), 都是用本地环境搭建  
## SQL Part - Microsoft SQL Server 
  ### 数据类型基础篇
  日期类型(Date,Datetime)的重要性,没有设计涉及转换函数,只是说这么用[Why use Data Type(Date Type)](Puny%20God/Why%20use%20Data%20Type(Date%20Type).md)
  数值类型,雪花函数,浮点小数以及货币类型.
  ### 多版本实现
  时态表(Temporal Table)<BR>
  参数化(Parameterization),使用存储过程(Store Produce)进行分装<BR>
  表值构造(Table Value Constructor)<BR>
  MERGE 构造方式,发现和Oracle还挺不同的<BR>
  ### 一些半结构类型
  Working

## KQL Azure Data Explore 

# Log
正经人不是很想写日志,或许是吃饱了
| YYYYMMDD | NVARCHAR(MAX) |
| ------------| ---- |
| 20220522    |虽然MERGE部分调一下,但感觉可以继续挖坑| 
| 20220521    |还是用自己的Surface Book当作服务器用Azure SQL Database/Azure SQL Managed Instance 无法进行暂停(Pasue)或Stop(停止)操作 |
| 20220520    |Azure SQL DW 没有资源，白嫖到一个Azure(Mooncake) 环境<BR> |
| 20220519    |最近手撸 Markdown开始熟练,能够写的话题也开始构思，只是因为写作环境和代码示例环境并不在笔记本电脑上,Fn Ctrl键位不同各种习惯|
|20220516     |Lockdown 情况由于Wahoo骑行台彻底不能用了，总要找些事情做。在这随便写写.Puny God 目录下主要用于吐槽供应商<BR>

# Preparations and Necessary Software
没有Cloud 环境，感觉自己下载安装软件的机会越来越少。<BR>
2022-05-16<BR>
以下写都写了，万一以后需要安装IaaS环境<BR>
- [x] [SQL Server 2019 Develop Enditon ](https://go.microsoft.com/fwlink/p/?linkid=866662)  
    Include  machine Learning and Language 
    Extensions   
    [Install a Python custom runtime for SQL Server](
https://docs.microsoft.com/zh-cn/sql/machine-learning/install/custom-runtime-python?view=sql-server-ver15&pivots=platform-windows
)  
- [x] Restore [SQL Server AdventureWorks sample databases](https://docs.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=ssms)  
- [x] [SQL Server Management Studio](https://docs.microsoft.com/zh-cn/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) 
  Azure Data Studio 
- [ ] [Oracle Database 19c(19.3) for Microsoft x64](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html)
- [ ] Install [Oracle Database 19c Examples (19.3) for Microsoft Windows x64 (64-bit)](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html)  
  下载但还没有安装
- [x] [Azure Data Studio Donwload and Configuration](https://docs.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio?view=sql-server-ver15)
    目前使用频率比SQL Server Management Studio 使用频率高<BR>
    基本可以适应为Visual Studio Code的数据库专用版本
    (SQL,R,Python)<BR>
    个人习惯喜欢将Run Query 以及 Run Current Query 执行快捷键修改为Alt+x
    [Keyboard shortcuts in Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/keyboard-shortcuts?view=sql-server-ver15#edit-existing-keyboard-shortcuts)
    Ctrl+K Ctrl+S 中文语言包环境下查找'运行查询'  
- [x] [Microsoft Power BI Desktop](https://www.microsoft.com/en-us/download/details.aspx?id=58494)  
编写[Power Query M Language](https://docs.microsoft.com/zh-cn/power-query/)  
编写DAX([Data Analysis Expressions](https://docs.microsoft.com/zh-cn/dax/dax-function-reference))用,可以不用安装Microsoft SQL Server Analysis Services Tabular Mode

- [x] 开始尝试研究下SPL([Structured Process Language](http://c.raqsoft.com.cn/article/1595816810031))



