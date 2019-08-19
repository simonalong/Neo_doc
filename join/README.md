# 十一、join

对于表join有如下这么几种类型<br />
![join.png](\img\join.png)

根据以上对于join的处理，这里新增了一个NeoJoiner类用于链式的拼接sql处理。以下为类Neo中的函数

```java
public NeoJoiner join(String leftTableName, String rightTableName){}
public NeoJoiner leftJoin(String leftTableName, String rightTableName){}
public NeoJoiner rightJoin(String leftTableName, String rightTableName){}
public NeoJoiner innerJoin(String leftTableName, String rightTableName){}
public NeoJoiner outerJoin(String leftTableName, String rightTableName){}
public NeoJoiner leftJoinExceptInner(String leftTableName, String rightTableName){}
public NeoJoiner rightJoinExceptInner(String leftTableName, String rightTableName){}
public NeoJoiner outerJoinExceptInner(String leftTableName, String rightTableName){}
```
NeoJoiner中也有跟上面一样的一组函数，且有on函数

```java
public NeoJoiner on(String leftColumnName, String rightColumnName){}
```

此外还有对数据的查询查询处理（还有更多处理）：

```java
public NeoMap one(Columns columns, NeoMap searchMap){}
public NeoMap one(Columns columns){}

public List<NeoMap> list(Columns columns, NeoMap searchMap){}
public List<NeoMap> list(Columns columns){}

public List<NeoMap> page(Columns columns, NeoMap searchMap, NeoPage neoPage) {}
public List<NeoMap> page(Columns columns, NeoPage page){}

public Integer count(Columns columns, NeoMap searchMap){}

public <T> T value(Class<T> tClass, String tableName, String columnName, NeoMap searchMap){}
public <T> T value(Class<T> tClass, String tableName, String columnName){}
public String value(String tableName, String columnName, NeoMap searchMap){}
public String value(String tableName, String columnName){}

public <T> List<T> values(Class<T> tClass, String tableName, String columnName, NeoMap searchMap){}
public <T> List<T> values(Class<T> tClass, String tableName, String columnName){}
public List<String> values(String tableName, String columnName, NeoMap searchMap){}
public List<String> values(String tableName, String columnName){}
```

<a name="7b1e8b49"></a>

<h3 id="两表join">两表join</h3>

基于以上的函数，我们这里简单列举 下简单的实例

```java
/**
 * join 采用的是innerJoin
 *
 * select neo_table1.`id` from neo_table1 
 * inner join neo_table2 on neo_table1.`id`=neo_table2.`n_id` 
 * order by sort desc limit 1
 */
/**
 * join 采用的是innerJoin
 *
 * select neo_table1.`id`  
 * from neo_table1 inner join neo_table2 on neo_table1.`id`=neo_table2.`id`  order by `sort` desc limit 1
 */
@Test
public void joinOneTest1() {
  String table1 = "neo_table1";
  String table2 = "neo_table2";
  show(neo.join(table1, table2).on("id", "id")
       .one(Columns.table(table1).cs("id"), NeoMap.of("order by", "sort desc")));
}

/**
 * join 采用的是innerJoin
 *
 * select neo_table1.`group`
 * from neo_table1
 * inner join neo_table2 on neo_table1.`id`=neo_table2.`n_id`
 */
@Test
public void joinListTest1() {
  String table1 = "neo_table1";
  String table2 = "neo_table2";
  // [{group=group1}, {group=group1}, {group=group2}, {group=group3}]
  show(neo.join(table1, table2).on("id", "n_id")
       .list(Columns.table(table1, "group")));
}

/**
 * 请注意，mysql 不支持 full join
 *
 * select neo_table2.`id`, neo_table1.`group` from neo_table2 
 * outer join neo_table1 on neo_table2.`n_id`=neo_table1.`id` 
 * order by sort desc
 */
@Test
public void outerJoinTest() {
  String table1 = "neo_table1";
  String table2 = "neo_table2";
  // [group3, group1, group2]
  show(neo.outerJoin(table1, table2).on("id", "n_id")
       .values(table1, "group", NeoMap.of("order by", "sort desc")));
}

/**
 * 测试多条件
 *
 * select neo_table1.`group`, neo_table1.`user_name`, neo_table1.`age`, neo_table1.`id`, neo_table1.`name`
 * from neo_table1 inner join neo_table2 on neo_table1.`id`=neo_table2.`n_id`
 * where neo_table1.`group` =  ? and neo_table1.`id` =  ? and neo_table2.`group` =  ? order by `sort` desc limit 1
 */
@Test
public void joinOneTest9() {
  String table1 = "neo_table1";
  String table2 = "neo_table2";
  // {group1=group3, id=13, name=name1, user_name=user_name1}
  show(neo.join(table1, table2).on("id", "n_id")
       .one(Columns.table(table1, neo).cs("*"), NeoMap.table(table1).cs("group", "group1", "id", 11)
            .and(table2).cs("group", "group1").append("order by", "sort desc")));
}

/**
 * join的分页查询
 *
 * select neo_table1.`group`, neo_table1.`user_name`, neo_table1.`age`, neo_table1.`id`, neo_table1.`name`
 * from neo_table1 inner join neo_table2 on neo_table1.`id`=neo_table2.`id`
 * order by neo_table1.`group` desc
 * limit 0, 12
 */
@Test
public void pageTest(){
  String table1 = "neo_table1";
  String table2 = "neo_table2";
  show(neo.join(table1, table2).on("id", "id")
       .page(Columns.table(table1, neo).cs("*"), NeoMap.table(table1).cs("order by", "group desc"),
             NeoPage.of(1, 12)));
}
```

<a name="b52f20a1"></a>

<h3 id="多表join">多表join</h3>

其实多表和两表是一样的，只是在on之后又多拼接了一个，举例如下

```java
/**
 * 多表join
 *
 * select neo_table1.`group`, neo_table1.`id`, neo_table2.`name`
 * from neo_table1 right join neo_table2 on neo_table1.`id`=neo_table2.`n_id`
 * right join neo_table3 on neo_table2.`name`=neo_table3.`name`    limit 1
 */
@Test
public void multiJoinTest() {
  String table1 = "neo_table1";
  String table2 = "neo_table2";
  String table3 = "neo_table3";
  show(neo.rightJoin(table1, table2).on("id", "n_id")
       .rightJoin(table2, table3).on("name", "name")
       .one(Columns.table(table1, "id", "group").and(table2, "name")));
}
```
<a name="Mdn2a"></a>

<h3 id="join类型">join类型</h3>

前面简单列举了，下面我们列举下对应的一些join

- join（在这里等价于left join）
- left join
- right join
- inner join
- outer join
- left join except inner
- right join except inner
- outer join except inner（mysql 不支持）

```java
/**
 * left join except inner
 *
 * 该处理的时候会自动增加一个条件，就是设置右表的主键为null，其实就是取的一个左表减去和右表公共的部分
 *
 * select neo_table1.`group`, neo_table1.`id`, neo_table2.`group`, neo_table1.`name`, neo_table2.`name`, neo_table2.`id`
 * from neo_table1 left join neo_table2 on neo_table1.`name`=neo_table2.`name`
 * where (neo_table2.id is null)  limit 1
 */
@Test
public void leftJoinExceptInnerTest(){
  String table1 = "neo_table1";
  String table2 = "neo_table2";
  show(neo.leftJoinExceptInner(table1, table2).on("name", "name")
       .one(Columns.table(table1, "id", "name", "group").and(table2, "id", "name", "group"))
      );
}
```

<a name="pWtK7"></a>

<h3 id="表别名处理">表别名处理</h3>
在单表时候查询数据时候还用不到表别名，但是对于多表join的时候，如果里面有表之间相互别名的时候，就需要用到别名了，那么别名怎么使用呢，这里涉及了这样的方式，直接将表名设置成“tablename as t1”，然后将这个直接作为表名进行查询即可。

```java
/**
 * 表的别名
 *
 * 这里要通过在表的前头直接填写即可
 *
 * select t1.`name`, t1.`id`, t1.`user_name`, t1.`group`, t1.`age`
 * from neo_table1 as t1 inner join neo_table2 as t2 on t1.`id`=t2.`id`
 * order by t2.`id` desc limit 0, 12
 */
@Test
public void tableAsTest1(){
  String table1 = "neo_table1 as t1";
  String table2 = "neo_table2 as t2";
  show(neo.join(table1, table2).on("id", "id")
       .page(Columns.table(table1, neo).cs("*"), NeoMap.table(table2).cs("order by", "id desc"),
             NeoPage.of(1, 12)));
}

/**
* 自己和自己
*
* select t2.`n_id`, t2.`age`, t2.`sort`, t2.`user_name`, t2.`name`, t2.`group`, t2.`enum1`, t2.`id`
* from neo_table3 as t1 left join neo_table3 as t2 on t1.`id`=t2.`n_id`
*
* 需要利用到别名系统才行
*/
@Test
public void joinSelfTest1(){
  String table1 = "neo_table3 as t1";
  String table2 = "neo_table3 as t2";
  neo.leftJoin(table1, table2).on("id", "n_id").list(Columns.table(table2, neo).cs("*"));
}
```
