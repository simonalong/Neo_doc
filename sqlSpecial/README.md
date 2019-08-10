# 十三、sql特殊处理

对于sql里面增加了一些特殊处理，模糊搜索和大小比较。
<a name="e3769034"></a>

<h3 id="sql模糊查询">1.sql模糊查询</h3>

在值中前面添加"like "即可，比如

```java
/**
 * 查询大小匹配的查询
 * 条件通过NeoMap设置
 * 相当于：select `group`, `name` from neo_table1 where `group` like 'group%'
 */
@Test
@SneakyThrows
public void testList10(){
  // select `group`, `name` from neo_table1 where `group` like 'group%'
  show(neo.list(TABLE_NAME, Columns.of("group", "name"), NeoMap.of("group", "like group")));
}
```

<a name="eb7b789a"></a>

<h3 id="sql大小比较查询">2.sql大小比较查询</h3>

在值中前面添加比较符号即可，比如

```java
/**
 * 查询大小匹配的查询
 * 条件通过NeoMap设置
 * 相当于：select `group`,`name` from neo_table1 where `name` < 'name' limit 1
 */
@Test
@SneakyThrows
public void testList9(){
  // select `group`, `name` from neo_table1 where `name` < ? ], {params => [name]
  show(neo.list(TABLE_NAME, Columns.of("group", "name"), NeoMap.of("name", "< name")));
  // select `group`, `name` from neo_table1 where `name` < ? ], {params => ['name']
  show(neo.list(TABLE_NAME, Columns.of("group", "name"), NeoMap.of("name", "< 'name'")));
  // select `group`, `name` from neo_table1 where `name` <= ? ], {params => [name']
  show(neo.list(TABLE_NAME, Columns.of("group", "name"), NeoMap.of("name", "<= name'")));
  // select `group`, `name` from neo_table1 where `name` > ? ], {params => ['name']
  show(neo.list(TABLE_NAME, Columns.of("group", "name"), NeoMap.of("name", "> 'name'")));
  // select `group`, `name` from neo_table1 where `name` >= ? ], {params => ['name']
  show(neo.list(TABLE_NAME, Columns.of("group", "name"), NeoMap.of("name", ">= 'name'")));
}
```