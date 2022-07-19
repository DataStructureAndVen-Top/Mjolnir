
时态表可以可以的很好跟踪客户数据


但客户往往需要知晓变化数据所对应列,差不多实现下面效果
![CHANGETABLE(CHANGES ...)](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/media/work-with-change-tracking-sql-server/query-output.png?view=sql-server-ver16)


作为关系型数据库, 列内容匹配对于SQL语句较为复杂,需要循环语句介入.假如不写使用结构化方式写循环,会有以下思路 


A 转换为半结构化[Document data stores](https://dev.mysql.com/doc/refman/5.7/en/year.html)<BR>, 使用全文匹配方式进行筛选
B 使用触发器方式[DML Tigger](https://docs.microsoft.com/zh-cn/sql/relational-databases/triggers/dml-triggers?view=sql-server-ver16)<BR>
C 使用[Change Data Capture](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/about-change-data-capture-sql-server?view=sql-server-ver16)进行捕获,进行业务逻辑处理<BR>
D 钝角  <BR>
E 使用[变动追踪](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/about-change-tracking-sql-server?view=sql-server-ver16)技术


A 会增加更多存储以及版本一致化问题,而且编写逻辑并不简便

之后的几种方案,都是数据产生过程进行标注

B 需要独立逻辑进行该功能模块开发,无法预测BULK INSERT[FIRE_TRIGGERS](https://docs.microsoft.com/zh-cn/sql/t-sql/statements/bulk-insert-transact-sql?view=sql-server-ver16#fire_triggers)所产生后果

C CDC 并不是为了该需求设计,CDC更多运作与数据分发环节,而非数据变更环节

D 反正一堆人都会选这个

E 官方功能,在达到目的情况下,性能最优解. 并且由于是官方功能,提供较多辅助性功能[Change Tracking Functions (Transact-SQL)](https://docs.microsoft.com/zh-cn/sql/relational-databases/system-functions/change-tracking-functions-transact-sql?view=sql-server-ver16)
以及规范性设计[Understanding Change Tracking Overhead](https://docs.microsoft.com/zh-cn/sql/relational-databases/track-changes/manage-change-tracking-sql-server?view=sql-server-ver16#understanding-change-tracking-overhead)