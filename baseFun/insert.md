# 增加

增加数据这里有如下这么几种方式

```
public NeoMap insert(String tableName, NeoMap valueMap) {}
public <T> T insert(String tableName, T entity) {}
public <T> T insert(String tableName, T entity, NamingChg naming) {}
```

<a name="mT4y2"></a>
<h4 id="参数详解">参数详解</h4>

- `tableName`：表名
- `valueMap`：是表中要插入的数据，其中`NeoMap`是自定义的`Map<String, Object>`结构，可实现多种常见的功能，详见下面的NeoMap介绍
- `NamingChg`：是实体`entity`的属性名字和`NeoMap`中`key`字段名字的相互映射，转换类型详见下面的介绍

| 参数 | 类型 | 详解 |
| --- | --- | --- |
| tableName | String | 表名 |
| valueMap | NeoMap | 待插入的数据 |
| entity | T | 待插入的数据，作为实体类型插入 |
| namingChg | NamingChg | 待插入的数据，通过该字符转换可以跟数据库字段对应上 |

**注意：**<br />其中`NeoMap`作为参数和`T entity`作为参数是一样的，下面的所有函数都是一样，`T entity`可以跟`NeoMap`相互转换，其中转换方式是`namingChg`，对于默认情况下，是`key`和`entity`的属性是完全对应的。
<a name="OzfgX"></a>
<h4 id="自增属性支持">自增属性支持</h4>

对于自增属性的支持，这里是默认支持，如果对应的表格的字段是主键且是自增字段，则插入之后，返回的值是含有生成的id的。<br />**比如：**

```sql
CREATE TABLE `neo_table1` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `group` char(64) COLLATE utf8_unicode_ci NOT NULL DEFAULT '' COMMENT '数据来源组，外键关联lk_config_group',
  `name` varchar(64) COLLATE utf8_unicode_ci NOT NULL DEFAULT '' COMMENT '任务name',
  `user_name` varchar(24) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '修改人名字',
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `group_index` (`group`)
) ENGINE=InnoDB AUTO_INCREMENT=15 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
**测试插入：**

```java
@Test
@SneakyThrows
public void testInsert1(){
  NeoMap result = neo.insert(TABLE_NAME, NeoMap.of("group", "ok"));
  // {group=ok, id=14, name=}
  show(result);
}
```
