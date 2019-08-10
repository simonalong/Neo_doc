# 其他查询

除了常见的查询之外，这里还提供了如下的查询：

<a name="74ZqM"></a>

<h3 id="表单独查询">a.表单独查询(*)</h3>

前面所有的查询都是通过传入一个表名，进而查询一个表的对应的信息，这里可以先获取一个表信息，然后就不需要再传入表名了，获取表对象的函数为

```java
public NeoTable getTable(String tableName){}
```
获取NeoTable对象之后，这个类是包含Neo中的相关的数据查询的<br />比如：<br />单行查询one

```java
// 不指定列，则查询所有的列
public NeoMap one(NeoMap searchMap){}
public <T> T one(T entity){}
public NeoMap one(Columns columns, NeoMap searchMap){}
public <T> T one(Columns columns, T entity){}
```
其中所有的函数跟Neo的都是一样的唯一的区别就是这里少了一个表名参数<br />以及其他的：

- 增加insert
- 删除delete
- 修改update
- 多行查询list
- 分页查询page
- 个数查询count
- 单个查询value
- 单列查询values
- 批量插入batchInsert（下面会介绍）
- 批量更新batchUpdate（下面会介绍）
<a name="41ana"></a>

<h3 id="In查询">b.In查询</h3>

针对常见的in的查询，这里也提供了一个专门构造sql的类SqlBuilder，里面有一个构造in语句的方法in()

```java
public <T> String in(List<T> values) {}
```

用法示例：
```java
/**
 * 查询一行数据
 * 采用直接执行sql方式，设定返回实体类型
 */
@Test
@SneakyThrows
public void testExeList6(){
  neo.setExplainFlag(true);
  List<Integer> idList = Arrays.asList(310, 311);
  // select * from neo_table1 where id in ('310','311')
  show(neo.exeList("select * from %s where id in %s", TABLE_NAME, SqlBuilder.in(idList)));
}
```