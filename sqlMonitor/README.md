# 九、sql监控

该框架是支持对于手写sql的，而手写sql很多时候无法保证sql的性能和规范化，因此这里增加一层监控，用于对sql进行监控和规范化告警。
<a name="1bdde129"></a>

<h3 id="sql耗时监控">1.sql耗时监控</h3>

这个是默认开启的，比如每个sql都会打印如下的debug日志

```sql
[Neo-monitor] [耗时: 5毫秒] [sql => select * from neo_table1 where `group` =  ?  limit 1], {params => [ok] }
```
这里系统设置：默认打印debug日志，一旦超过3秒，则打印info日志，超过10秒，打印warn日志，超过1分钟，则打印error日志。
<a name="fb978020"></a>

<h3 id="sql规范化监控">2.sql规范化监控</h3>

默认开启<br />对于sql规范化参考自阿里巴巴的规范化手册中的sql部分和网上部分，然后对其中可以落地的进行的规范进行落地汇总。

| 名字 | 描述 | 告警类别 |
| --- | --- | --- |
| SELECT | 请不要使用select *，尽量使用具体的列 | warn |
| WHERE_NOT | where子句中，尽量不要使用，!=,<>,not,not in, not exists, not like | warn |
| LIKE | where子句中，like 这里的模糊请尽量不要用通配符%开头匹配，即like '%xxx' | warn |
| IN | where子句中有in操作，请谨慎使用，防止in中数据超过1千行 | info |
| UPDATE_NO_WHERE | update 更新语句中，没有where子句 | warn |

除了系统内置的之外，还可以在Neo类中自己添加额外的监控

```java
/**
* 添加自定义规范
*
* @param regex 正则表达式
* @param desc 命中之后的文本打印
* @param logType 日志级别
*/
public void addStandard(String regex, String desc, LogType logType){
  standard.addStandard(regex, desc, logType);
}
```

<a name="586bd541"></a>

<h3 id="sql语句优化监控">3.sql语句优化监控</h3>

sql语句监控是利用的mysql的校验特性，即在每个sql前面添加explain关键字，在sql执行前先执行一次explain（我们的所有sql都是含有占位符的，执行一次之后，就将该sql保存起来，后面每次执行只打印第一次的测量结果），然后根据测量的结果进行打印出来，目前打印告警有三种：

- sql走了全表扫描：warn日志
- sql走了全索引扫描：info日志
- sql其他索引类型：debug日志

explain打印的字段如下，我们只关注type<br />![image.png](https://cdn.nlark.com/yuque/0/2019/png/126182/1557157392018-41f10b8e-7f15-4030-9c51-f12e8ed0ff01.png#align=left&display=inline&height=67&name=image.png&originHeight=126&originWidth=1382&size=22297&status=done&width=733)<br />其中主要衡量对象是type，这是一个非常重要的参数，连接类型，常见的有：all , index , range , ref , eq_ref , const , system , null 八个级别。性能从最优到最差的排序：system > const > eq_ref > ref > range > index > all，我们这里约束要至少达到range级别或者最好能达到ref，all进行告警，index进行Info打印，其他的进行debug打印。

| type | 说明 |
| --- | --- |
| all | （full table scan）全表扫描无疑是最差，若是百万千万级数据量，全表扫描会非常 |
| index | （full index scan）全索引文件扫描比all好很多，毕竟从索引树中找数据，比从全表中找数据 |
| range | 只检索给定范围的行，使用索引来匹配行。范围缩小了，当然比全表扫描和全索引文件扫描要快。sql语句中一般会有between，in，>，< 等查询。 |
| ref | 非唯一性索引扫描，本质上也是一种索引访问，返回所有匹配某个单独值的行。比如查询公司所有属于研发团队的同事，匹配的结果是多个并非唯一值。 |
| eq_ref | 唯一性索引扫描，对于每个索引键，表中有一条记录与之匹配。比如查询公司的CEO，匹配的结果只可能是一条记录 |
| const | 表示通过索引一次就可以找到，const用于比较primary key 或者unique索引。因为只匹配一行数据，所以很快，若将主键至于where列表中，MySQL就能将该查询转换为一个常量 |
| system | 表只有一条记录（等于系统表），这是const类型的特列，平时不会出现，了解即可 |
