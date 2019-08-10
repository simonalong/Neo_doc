# 查询

在查询这里，增加了这么几类函数（有部分是借鉴别的框架）：

- 单行查询one
- 多行查询list
- 分页查询page
- 个数查询count
- 单个value
- 单列多值values
- 直接执行sql
  - 直接执行
  - 执行获取单行
  - 执行获取多行
  - 执行获取个数
  - 执行获取单个值
  - 执行获取单列多值
<a name="IwMpH"></a>

<h3 id="单行查询one">a.单行查询one</h3>

```java
public NeoMap one(String tableName, NeoMap searchMap){}
public <T> T one(String tableName, T entity){}
public NeoMap one(String tableName, Columns columns, NeoMap searchMap) {}
public <T> T one(String tableName, Columns columns, T entity){}
```

| 参数 | 类型 | 详解 |
| --- | --- | --- |
| tableName | String | 表名 |
| searchMap | NeoMap | where条件后面的搜索条件 |
| entity | T | where条件后面的搜索条件，实体形式 |
| columns | Columns | 返回结果中指定的列 |

**注意：**<br />1.one执行的时候会自动在sql语句最后添加`limit 1`<br />2.（重要）在搜索条件中对于排序可以放到搜索条件中，如下，其中排序适用于后面所有的查询

```java
/**
 * 测试order by
 */
@Test
public void testOrderBy1(){
  // select `name` from neo_table1 where `group` =  ? order by `name` desc
  show(neo.list(TABLE_NAME, Columns.of("name"), NeoMap.of("group", "g", "order by", "name desc")));
}

@Test
public void testOrderBy2(){
  // select `name` from neo_table1 where `group` =  ? order by `name` desc, `group` asc  limit 1
  show(neo.list(TABLE_NAME, Columns.of("name"), NeoMap.of("group", "g", "order by", "name desc, group asc")));
}

@Test
public void testOrderBy3(){
  // select `name` from neo_table1 where `group` =  ? order by `name`, `group` desc, `id` asc  limit 1
  show(neo.list(TABLE_NAME, Columns.of("name"), NeoMap.of("group", "g", "order by", "name, group desc, id asc")));
}
```

<a name="mkT1Z"></a>

<h3 id="多行查询list">b.多行查询list</h3>

多行查询函数的参数跟单行查询的函数是相同的

```java
public <T> List<T> list(String tableName, T entity){}
public List<NeoMap> list(String tableName, NeoMap searchMap){}
public <T> List<T> list(String tableName, Columns columns, T entity){}
public List<NeoMap> list(String tableName, Columns columns, NeoMap searchMap) {}
```
参数同`one`
<a name="XziYV"></a>

<h3 id="分页查询page">c.分页查询page</h3>

```java
public List<NeoMap> page(String tableName, Columns columns, NeoMap searchMap, NeoPage page) {}
public <T> List<T> page(String tableName, Columns columns, T entity, NeoPage page){}
public List<NeoMap> page(String tableName, NeoMap searchMap, NeoPage page){}
public <T> List<T> page(String tableName, T entity, NeoPage page){}
public List<NeoMap> page(String tableName, Columns columns, NeoPage page){}
public List<NeoMap> page(String tableName, NeoPage page){}
```

| 参数 | 类型 | 详解 |
| --- | --- | --- |
| tableName | String | 表名 |
| searchMap | NeoMap | where条件后面的搜索条件 |
| entity | T | where条件后面的搜索条件，实体形式 |
| columns | Columns | 返回结果中指定的列 |
| page | NeoPage | 内部包含pageIndex, pageSize, pageNo |

<a name="Pyv0m"></a>

<h3 id="个数查询count">d.个数查询count</h3>

针对个数的查询有下面这个几个函数

```java
public Integer count(String tableName, NeoMap searchMap) {}
public Integer count(String tableName, Object entity) {}
public Integer count(String tableName) {}
```

<a name="nekuR"></a>

<h3 id="单值查询value">e.单值查询value</h3>

针对这个查询，其实就是查询某一行中的某个列的值

```java
public String value(String tableName, String field, Object entity) {}
public String value(String tableName, String field, NeoMap searchMap){}
public <T> T value(Class<T> tClass, String tableName, String field, NeoMap searchMap) {}
public <T> T value(Class<T> tClass, String tableName, String field, Object entity) {}
```

| 参数 | 类型 | 详解 |
| --- | --- | --- |
| tClass | Class | 返回值的Class类型 |
| tableName | String | 表名 |
| field | String | 要查询的列名 |
| entity | T | where条件后面的搜索条件，实体形式 |
| searchMap | NeoMap | where条件后面的搜索条件 |

<a name="w9ebU"></a>

<h3 id="单列多值查询values">f.单列多值查询values</h3>

单列多值的函数为`values`，对应的参数和上面介绍的`value`相同
<a name="ieNE8"></a>

<h3 id="直接执行sql">g.直接执行sql</h3>

直接执行这里面除了直接执行数据之外，还可以执行获取单列，单值，多列，个数等
<a name="jkBKk"></a>

<h4 id="直接执行">直接执行</h4>

```java
public List<List<NeoMap>> execute(String sql, Object... parameters) {}
```


sql是包含java的String的格式化转换符和JDBC的占位符"?"的，比如

```java
neo.execute("update %s set `group`=?, `name`=%s where id = ?", TABLE_NAME, "group121", "'name123'", 121)
```

**占位符：**<br />java的转换符：

| 转换符 | 说明 | 示例i |
| --- | --- | --- |
| %s | 字符串类型 | "mingrisoft" |
| %c | 字符类型 | 'm' |
| %b | 布尔类型 | true |
| %d | 整数类型（十进制） | 99 |
| %x | 整数类型（十六进制） | FF |
| %o | 整数类型（八进制） | 77 |
| %f | 浮点类型 | 99.99 |
| %a | 十六进制浮点类型 | FF.35AE |
| %e | 指数类型 | 9.38e+5 |
| %g | 通用浮点类型（f和e类型中较短的） |   |
| %h | 散列码 |   |
| %% | 百分比类型 | ％ |
| %n | 换行符 |   |
| %tx | 日期与时间类型（x代表不同的日期与时间转换符 |   |

注意：<br />该execute不支持多语句执行，如果想执行多语句，则可以用事务方式，参考下面的tx，或者可以通过多结果集的方式，举例如下：

```java
/**
 * 测试多结果集
 * CREATE PROCEDURE `pro`()
 * BEGIN
 *   explain select * from neo_table1;
 *   select * from neo_table1;
 * END
 */
@Test
public void testExecute5(){
  show(neo.execute("call pro()"));
}
```


<a name="cF726"></a>

<h4 id="执行获取单行">执行获取单行</h4>

执行单行其实就是在sql的最后添加limit 1，并返回唯一一个结果实体

```java
public NeoMap exeOne(String sql, Object... parameters) {}
public <T> T exeOne(Class<T> tClass, String sql, Object... parameters){}
```
<a name="JJOwD"></a>

<h4 id="执行获取多行">执行获取多行</h4>

```java
public List<NeoMap> exeList(String sql, Object... parameters) {}
public <T> List<T> exeList(Class<T> tClass, String sql, Object... parameters){}
```
<a name="rg4VA"></a>

<h4 id="执行获取分页">执行获取分页</h4>

```java
public List<NeoMap> exePage(String sql, Integer startIndex, Integer pageSize, Object... parameters){}
public List<NeoMap> exePage(String sql, NeoPage neoPage, Object... parameters){}
```
<a name="32o96"></a>

<h4 id="执行获取个数">执行获取个数</h4>

```java
public Integer exeCount(String sql, Object... parameters) {}
```
<a name="tU5Lh"></a>

<h4 id="执行获取单个值">执行获取单个值</h4>

```java
public String exeValue(String sql, Object... parameters){}
public <T> T exeValue(Class<T> tClass, String sql, Object... parameters) {}
```
<a name="GAzwT"></a>

<h4 id="执行获取单列多值">执行获取单列多值</h4>

```java
public List<String> exeValues(String sql, Object... parameters){}
public <T> List<T> exeValues(Class<T> tClass, String sql, Object... parameters) {}
```
