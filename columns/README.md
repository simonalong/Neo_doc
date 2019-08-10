# 七、Columns类

在代码内部这个类比较常见，就单独讲解一下，该类主要是用于对列进行处理并将普通的字符转换为sql所需要的格式。<br />普通的列名经过这里处理之后是要变成这样的格式：

```
name -> `name`
table.name -> table.`name`
name as n -> `name` as n
name n -> `name` n
table.name as n -> table.`name` as n
```

<a name="doqyv"></a>

<h3 id="初始化">1.初始化</h3>

```java
public static Columns of(String... fields) {}
public static Columns of(List<Field> fieldList) {}

// 获取表tableName的所有列名
public static Columns from(Neo neo, String tableName) {}
// 获取某个类的所有列
public static Columns from(Class tClass) {}
// 根据类型转换获取类的属性转换
public static Columns from(Class tClass, NamingChg namingChg) {}

// 设置列的前缀，和cs(String... cs)结合使用，用于表示列的前缀
public static Columns table(String tableName){}
public static Columns table(String tableName, Neo neo){}
```

<a name="aA4HO"></a>

<h3 id="多表的处理">2.多表的处理</h3>

在使用多表的时候，我们可以直接使用of函数，将所有的列放进去，不过也可以使用下面的方式

```java
public static Columns table(String tableName){}
public static Columns table(String tableName, Neo neo){}

public Columns cs(String... cs){}
public Columns cs(Set<String> fieldSets) {}
```

```java
@Test
public void tableCsTest1() {
  // tableName.`c2`, tableName.`c3`, tableName.`c1`
  Assert.assertEquals(Columns.of("tableName.`c2`", "tableName.`c3`", "tableName.`c1`"),
                      Columns.table("tableName").cs("c1", "c2", "c3"));
}

/**
 * 多表的数据字段
 */
@Test
public void tableCsTest3() {
  String table1 = "table1";
  String table2 = "table2";
  String table3 = "table3";
  Columns columns = Columns.table(table1).cs("a1", "b1")
    .and(table2).cs("a2", "b2")
    .and(table3).cs("a3", "b3");

  // table3.`b3`, table1.`b1`, table1.`c1`, table1.`a1`, table2.`c2`, table2.`b2`, table2.`a2`, table3.`a3`
  show(columns.buildFields());
  Assert.assertEquals(Columns.of("table1.`a1`", "table1.`b1`", "table2.`a2`", "table2.`b2`", "table3.`a3`", "table3.`b3`"), columns);
}
```

<a name="SV7db"></a>

<h3 id="表所有列处理">3.表所有列处理</h3>

针对需要表的所有列的时候，平常是用"*"，但是sql规范中是不建议使用"*"，我们可能需要手写，而手写又太多了，那么我们这里增加这样的写法，在使用符号“*”的时候要传入库对象Neo，否则会报异常。

```java
/**
* 获取所有的列名 "*"
*/
@Test
public void allColumnTest1() {
  // neo_table1.`group`, neo_table1.`user_name`, neo_table1.`age`, neo_table1.`id`, neo_table1.`name`
  Columns columns = Columns.table("neo_table1", neo).cs("*");

  Assert.assertEquals(Columns.of("neo_table1.`group`", "neo_table1.`user_name`", "neo_table1.`age`", "neo_table1.`id`", "neo_table1.`name`"),
                      columns);
}

/**
 * 获取所有的列名 "*"，有多余的列，则会覆盖
 */
@Test
public void allColumnTest2() {
  // neo_table1.`group`, neo_table1.`user_name`, neo_table1.`age`, neo_table1.`id`, neo_table1.`name`
  Columns columns = Columns.table("neo_table1", neo).cs("*", "group");
  Assert.assertEquals(Columns.of("neo_table1.`group`", "neo_table1.`user_name`", "neo_table1.`age`", "neo_table1.`id`", "neo_table1.`name`"), columns);
}

/**
 * 获取所有的列名 "*"，如果有别名，则以别名为主
 */
@Test
public void allColumnTest3() {
  // neo_table1.`user_name`, neo_table1.`age`, neo_table1.`group` as g, neo_table1.`id`, neo_table1.`name`
  Columns columns = Columns.table("neo_table1", neo).cs("*", "group as g");
  Assert.assertEquals(Columns.of("neo_table1.`group` as g", "neo_table1.`user_name`", "neo_table1.`age`", "neo_table1.`id`", "neo_table1.`name`"), columns);
}
```

<a name="8jZmx"></a>

<h3 id="列别名处理">4.列别名处理</h3>

针对别名处理的时候，我们这里支持这么两种方式：as 方式和空格方式

```java
@Test
public void aliasTest1(){
  // `c1` s c11, `c2`, `c3`
  show(Columns.of("`c1` as c11", "`c2`", "`c3`"));
}

@Test
public void aliasTest2(){
  // `c1` c11, `c2`, `c3`
  show(Columns.of("c1 c11", "`c2`", "`c3`"));
}

@Test
public void aliasTest3(){
  // `c1`  c11, `c3`
  show(Columns.of("c1  c11", "`c1`", "`c3`").buildFields());
}

@Test
public void aliasTest4(){
  // table.`c1`  c11, `c3`
  show(Columns.of("table.c1  c11", "`c1`", "`c3`").buildFields());
}
```

别名中，我们会将有别名的列和对应的列认为是相同的列

<a name="rmc4l"></a>
### 4.表别名处理
针对别名处理的时候，我们这里支持这么两种方式：as 方式和空格方式

```java
@Test
public void aliasTest1(){
  // `c1` s c11, `c2`, `c3`
  show(Columns.of("`c1` as c11", "`c2`", "`c3`"));
}

@Test
public void aliasTest2(){
  // `c1` c11, `c2`, `c3`
  show(Columns.of("c1 c11", "`c2`", "`c3`"));
}

@Test
public void aliasTest3(){
  // `c1`  c11, `c3`
  show(Columns.of("c1  c11", "`c1`", "`c3`").buildFields());
}

@Test
public void aliasTest4(){
  // table.`c1`  c11, `c3`
  show(Columns.of("table.c1  c11", "`c1`", "`c3`").buildFields());
}
```

别名中，我们会将有别名的列和对应的列认为是相同的列
