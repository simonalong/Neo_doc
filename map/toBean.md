# NeoMap和JavaBean转换

<h3 id="对象转换">1.对象转换</h3>

```java
// Neo转到指定的实体，默认风格转换器就是不转换
public <T> T as(Class<T> tClass) {}

// Neo转到指定的实体，但是要在指定的风格转换下
public <T> T as(Class<T> tClass, NamingChg naming) {}
```

<a name="XY2EP"></a>

<h3 id="集合转换">2.集合转换</h3>


```java
// List<NeoMap>转换到集合List<T>
public static <T> List<T> asArray(List<NeoMap> neoMaps, Class<T> tClass) {}

// List<NeoMap>根据指定的风格进行转换到集合List<T>
public static <T> List<T> asArray(List<NeoMap> neoMaps, Class<T> tClass, NamingChg namingChg) {}

// List<T> 转换到List<NeoMap>
public static <T> List<NeoMap> fromArray(List<T> dataList) {}

// List<T> 根据指定风格转换到List<NeoMap>
public static <T> List<NeoMap> fromArray(List<T> dataList, NamingChg namingChg) {}

// List<T> 指定一些列转换到List<NeoMap>
public static <T> List<NeoMap> fromArray(List<T> dataList, Columns columns) {}

// List<T> 根据指定风格将指定的列转换到List<NeoMap>
public static <T> List<NeoMap> fromArray(List<T> dataList, Columns columns, NamingChg namingChg) {}
```

<a name="wasZ4"></a>
