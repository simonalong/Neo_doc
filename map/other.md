# 其他功能

<h3 id="合并更多数据">1.合并更多数据</h3>

```java
// 关联另外的NeoMap跟putAll一样
public NeoMap append(NeoMap neoMap) {}

// 增加数据，跟put一样，只是返回值是NeoMap，便于链式操作
public NeoMap append(String key, Object value) {}

// 跟putAll差不多，不过这里新增了命名转换器，用于key的转换
public NeoMap putAll(NeoMap sourceMap, NamingChg namingChg) {}
```

<a name="itpW6"></a>

<h3 id="NeoMap获取固定列">2.NeoMap获取固定列</h3>

```java
// 这是一个NeoMap只保留指定的列，并生成一个新的NeoMap
public NeoMap assign(Columns columns) {}
```

<a name="aDjIj"></a>

<h3 id="NeoMap列添加前缀">3.NeoMap列添加前缀</h3>

有些时候我们需要给NaoMap中的列添加一些前缀

```java
// 给所有的key添加前缀，比如给所有的列添加"x_"
public NeoMap setKeyPre(String preFix) {}
```

<a name="piU9k"></a>

<h3 id="NeoMap列转换">4.NeoMap列转换</h3>

key进行转换

```java
// 对NeoMap中的key进行转换，keys：oldKey-newKey-oldKey-newKey-...
public NeoMap keyConvert(String... keys) {}
```

<a name="WWfQm"></a>

<h3 id="判空">5.判空</h3>

判空这里可以对NeoMap判空也可以对集合判空

```java
// 集合判空
public static boolean isEmpty(Collection<NeoMap> neoMaps) {
	return neoMaps == null || neoMaps.isEmpty() || neoMaps.stream().allMatch(Map::isEmpty);
}

// NeoMap判空
public static boolean isEmpty(NeoMap neoMap) {
  return neoMap == null || neoMap.isEmpty();
}
```

<a name="NInFl"></a>

<h3 id="获取不同的列值类型">6.获取不同的列值类型</h3>

由于NeoMap的value是Object类型，因此获取值之后还是要进行转换，这里借鉴之前的Orm框架，根据类型获取不同类型的值

```java
// 根据指定的类型获取对应的数据
public <T> T get(Class<T> tClass, String key){}

// 获取空则有默认值，下面所有的类型都有两个函数，这里的这个默认值后面不再写了
public <T> T get(Class<T> tClass, String key, T defaultValue){}

public Character getCharacter(String key){}
public String getStr(String key){}
public Boolean getBoolean(String key) {}
public Byte getByte(String key){}
public Short getShort(String key){}
public Integer getInteger(String key){}
public Long getLong(String key){}
public Double getDouble(String key){}
public Float getFloat(String key){}

// 获取集合对象，注意：对于原先对象为非tClass的List，如果可以转为tClass，那么这里也是支持的，下面有解释
public <T> List<T> getList(Class<T> tClass, String key){}
// 获取集合Set对象，同上
public <T> Set<T> getSet(Class<T> tClass, String key){}
// 将值获取为NeoMap，对于value为普通对象的也可以用该方法，会自动的转换为NeoMap
public NeoMap getNeoMap(String key){}
```

对于上面的获取，其中需要注意的是，对于有Class<T> tClass参数的函数，其中tClass可以不是原先存储的类型，只要原类型可以转换为的类型即可，如下例子

```java
/**
 * 其中原存储类型是String，但是按照Integer类型获取依然可以获取到
 */
@Test
public void getClassTest1(){
  NeoMap neoMap = NeoMap.of("a", "1", "b", "2");
  Integer result = neoMap.get(Integer.class, "a");
	Assert.assertTrue(result.equals(1));
}

/**
 * getNeoMap除了返回值可以为NeoMap之外，还可以是普通对象
 */
@Test
public void getNeoMapTest2(){
  DemoEntity demoEntity = new DemoEntity().setName("name").setId(12L).setUserName("user");

  NeoMap data = NeoMap.of("a", NeoMap.of("name", "name", "id", 12L, "userName", "user"));
  Assert.assertTrue(demoEntity.equals(data.get(DemoEntity.class, "a")));
}

/**
 * getNeoMap除了返回值可以为实体对象外，还可以是NeoMap
 */
@Test
public void getNeoMapTest3(){
  DemoEntity demoEntity = new DemoEntity().setName("name").setId(12L).setUserName("user");

  NeoMap data = NeoMap.of("a", demoEntity);

  NeoMap result = NeoMap.of("name", "name", "id", 12L, "userName", "user");
  Assert.assertTrue(result.equals(data.get(NeoMap.class, "a")));
}

/**
 * 对于枚举类型，原值为String也可以获取到枚举类型
 */
@Test
public void getEnumTest2(){
  NeoMap neoMap = NeoMap.of("enum", "A1");
  Assert.assertEquals(EnumEntity.A1, neoMap.get(EnumEntity.class, "enum"));
}
```
其中其他的几个跟Class有关的函数也是一样的，也是可以转换为对应的兼容类型的，比如：
> getList， getSet，getNeoMap

兼容类型：

| 原类型 | 可以兼容的类型 | 说明 |
| --- | --- | --- |
| NeoMap | 自定义类型 | 自定义类型里面属性只要包含NeoMap中的key相同即可 |
| 自定义类型 | NeoMap |  |
| 非String的基本类型 | String |  |
| String | 枚举类型 | 只要值和枚举中的值相同即可转换 |
| String | Boolean | 只有原类型的值为true或者false才行 |
| String | Character | 取String的第一个字符 |
| String | Byte, Short, Integer, Long, Float, Double | 字符串是数字字符才可转换 |
| 非集合，非枚举，非数组的普通类型 | NeoMap | 对应的就是getNeoMap()函数 |
| java.sql.Date,<br />java.sql.Time,<br />java.sql.TimeStamp, <br />java.utl.Date | Long | 对应函数：get(Long.class, String key)和getLong(String key) |

<a name="mraxd"></a>

<h3 id="多表的列使用">7.多表的列使用</h3>

在下面介绍的join中有种场景是多表关联，而多表关联，则需要用到多个表的搜索条件，那么多表的搜索条件应该如何使用，如果按照上面的通常情况下，那么就可能会像这样使用。

```java
NeoMap.of("table1.`name`", "a", "table1.`age`", 123, "table2.`group`", "g1", "table3.`name`", "k");
```
我们可以看到，在列和表更多的时候，写起来特别麻烦，而且里面很多key的字段有些都是相同的，那么因此就做了如下的设计，下面的效果与上面的完全一致

```java
NeoMap.table(table1).cs("name", "a", "age", 123)
            .and(table2).cs("group", "g1")
            .and(table3).cs("name", "k");
```

新增了静态函数和几个非静态函数，全部都支持链式写法

```java
public static NeoMap table(String tableName){}
public NeoMap cs(Object... kvs) {}
public NeoMap and(String tableName){}
```
