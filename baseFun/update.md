# 修改

对数据库中的数据进行修改

```java
/**
 * 数据更新
 * @param tableName 表名
 * @param dataMap set的更新的数据
 * @param searchMap where后面的语句条件数据
 * @return 更新之后的返回值
 */
public NeoMap update(String tableName, NeoMap dataMap, NeoMap searchMap) {}
public <T> T update(String tableName, T setEntity, NeoMap searchMap, NamingChg namingChg) {}
public <T> T update(String tableName, T setEntity, NeoMap searchMap) {}
public <T> T update(String tableName, T setEntity, T searchEntity) {}
public NeoMap update(String tableName, NeoMap dataMap, Columns columns) {}
public <T> T update(String tableName, T entity, Columns columns, NamingChg namingChg) {}
public <T> T update(String tableName, T entity, Columns columns) {}

public NeoMap update(String tableName, NeoMap dataMap) {}
public <T> T update(String tableName, T entity) {}

public <T> T update(String tableName, T entity, Columns columns) {}
public <T> T update(String tableName, T entity, Columns columns, NamingChg namingChg) {}
```
`update`的重载函数有点多，但是终究只是对`update`对应的sql的简单拼装，其中对应的参数如下

| 参数 | 类型 | 详解 |
| --- | --- | --- |
| tableName | String | 表名 |
| dataMap | NeoMap | update待更新的数据 |
| setEntity | T | update待更新的数据，跟dataMap一样，只是这里是实体形式 |
| searchMap | NeoMap | update更新的条件，用于在where后面的子句中 |
| searchEntity | T | update更新的条件，用于在where后面的子句中，这里是实体形式 |
| columns | Columns | 指定的在dataMap数据的对应的列，以节省再输入的麻烦，可以为NeoMap指定列，也可以为entity实体指定属性 |
| namingChg | NamingChg | 待插入的数据，通过该字符转换可以跟数据库字段对应上 |

例子：

```java
@Test
@SneakyThrows
public void testUpdate1(){
  // update neo_table1 set `group`=? where `group` =  ? and `name` =  ?
  neo.update(TABLE_NAME, NeoMap.of("group", "ok2"), NeoMap.of("group", "group2", "name", "name"));
}

@Test
@SneakyThrows
public void testUpdate2(){
  // update neo_table1 set `group`=?, `name`=? where `name` =  ?
  show(neo.update(TABLE_NAME, NeoMap.of("group", "ok3", "name", "name"), Columns.of("name")));
}

@Test
@SneakyThrows
public void testUpdate3(){
  DemoEntity input = new DemoEntity();
  input.setGroup("group2");
  // update neo_table1 set `group`=? where `group` =  ? and `name` =  ?
  show(neo.update(TABLE_NAME, input, NeoMap.of("group", "group1", "name", "name")));
}

@Test
@SneakyThrows
public void testUpdate4(){
  DemoEntity search = new DemoEntity();
  search.setGroup("group1");

  DemoEntity data = new DemoEntity();
  data.setGroup("group2");
  // update neo_table1 set `group`=? where `group` =  ?
  show(neo.update(TABLE_NAME, data, search));
}

/**
     * 指定某个列作为查询条件
     */
@Test
@SneakyThrows
public void testUpdate5(){
  // update neo_table1 set `group`=?, `name`=? where `group` =  ?
  show(neo.update(TABLE_NAME, NeoMap.of("group", "group1", "name", "name2"), Columns.of("group")));
}

/**
     * 指定某个列作为查询条件
     */
@Test
@SneakyThrows
public void testUpdate8(){
  DemoEntity search = new DemoEntity();
  search.setGroup("group555");
  search.setName("name333");
  // update neo_table1 set `group`=?, `name`=? where `name` =  ?
  show(neo.update(TABLE_NAME, search, Columns.of("name")));
}

/**
     * 指定某个列作为查询条件
     */
@Test
@SneakyThrows
public void testUpdate9(){
  DemoEntity search = new DemoEntity();
  search.setGroup("group555");
  search.setName("name333");
  search.setUserName("userName2222");
  show(neo.update(TABLE_NAME, search, Columns.of("userName"), NamingChg.UNDERLINE));
}
```

**注意：**<br />对于如下两个函数，可以看到没有指定搜索条件，对于这种，这里采用的是，如果dataMap和entity中含有主键，则默认会将该主键设置为后面的搜索条件，比如下面的例子

```java
public NeoMap update(String tableName, NeoMap dataMap) {}
public <T> T update(String tableName, T entity) {}
```

```java

/**
 * 不指定则，默认查找搜索条件中的主键列对应的值为搜索条件
 */
@Test
@SneakyThrows
public void testUpdate6(){
  // update neo_table1 set `group`=?, `id`=?, `name`=? where `id` =  ?
  show(neo.update(TABLE_NAME, NeoMap.of("id", 2, "group", "group222", "name", "name2")));
}

/**
 * 指定某个列作为查询条件，默认查找搜索条件中的主键列对应的值为搜索条件
 */
@Test
@SneakyThrows
public void testUpdate7(){
  DemoEntity search = new DemoEntity();
  search.setId(281L);
  search.setGroup("group555");
  // update neo_table1 set `group`=?, `id`=? where `id` =  ?
  show(neo.update(TABLE_NAME, search));
}
```