发现还是挖坑比较开心,主要是挖了的坑不用填这样没有心理负担<BR>
看空闲状况,写到哪里算哪里<BR>

Kusto Query Language (KQL)目前也就官方资料比较齐全, 网上大多数文档都是Copy-Paste。所以原创还是发到Github去吧<BR>


KSQL创建的目的,官方也有阐述,为了[探索数据、发现模式、识别异常和离群值、创建统计建模](https://docs.microsoft.com/zh-cn/azure/data-explorer/kusto/query/),不是说其他语言不能干,而在很多语言设立之初并没有考虑,或同通过增补集合方式提供比较别扭的功能(SQL),或数据量增加够性能无妨进行满足(Python)<BR>
掌握新语言需要花费时间练习, 以及抛弃一些固有思维过程<BR>

微软提供语言转化资料比较有意思, 尝试把各种类型统一语言查询<BR>
[Reference material for Kusto Query Language](https://docs.microsoft.com/zh-cn/azure/data-explorer/kusto/query/re2) <BR>
| 转换为Kusto            | 自己理解                 |
| ---------------------- | ------------------------ |
| RE2 语法与 RE2库       | 正则表达                 |
| JSONPath 语法          | 半结构结构               |
| SQL 到Kusto 备份单     | 结构化                   |
| Splunk 到 Kusto 备份单 | 文本                     |
| 时区                   | 省的你们自己折腾timezone |
<BR>

目前可以使用Kusto 的产品有Microsoft Purview(Mooncake还未上线) 与 [Azure Data Explorer](https://www.azure.cn/pricing/details/data-explorer/)<BR>
当然只要Microsoft 帐户(邮箱) 就有免费的资源可以用(当然是Global资源) https://aka.ms/kustofree <BR>
[What is a free Azure Data Explorer cluster?](https://docs.microsoft.com/zh-cn/azure/data-explorer/start-for-free)<BR>
引入示例数据在这里https://docs.microsoft.com/zh-cn/azure/data-explorer/ingest-sample-data?tabs=one-click-ingest<BR>
公司貌似使用[Kusto.Explorer](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/tools/kusto-explorer)有问题,目前就web方式将就使用



