# NeoMap初始化

<h2 id="NeoMap初始化">1.NeoMap初始化</h2>

NeoMap初始化方式很多，使得Map的使用更加的方便

```java
// 根据key-value-key-value...这种初始化，key为String
public static NeoMap of(Object... kvs) {}

// 根据对象直接获取
public static NeoMap from(Object object) {}

// 根据对象转换获取，其中namingChg是将对象的属性转换为DB中的字段风格字段
public static NeoMap from(Object object, NamingChg namingChg) {}

// 指定对象中的某些属性放到NeoMap中
public static NeoMap from(Object object, Columns columns) {}

// 指定对象中的某些属性，同时也指定对象的属性名字和NeoMap的转换，其中NeoMap的key默认是跟DB中的字段对应
public static NeoMap from(Object object, Columns columns, NamingChg namingChg) {}

// 对象的转换，其中userDefineNaming是用于自定的对象属性和转换到NeoMap中的属性
public static NeoMap from(Object object, NeoMap userDefineNaming) {}

// 对象中指定某些属性转换，跟上面的参数columns一个效果
public static NeoMap fromInclude(Object object, String... fields) {}

// 对象中排除某些属性转换
public static NeoMap fromExclude(Object object, String... fields) {}

// 对象中指定某些属性，且排除某些属性转换
public static NeoMap from(Object object, NeoMap userNaming, List<String> inFieldList, List<String> exFieldList) {}

// 对象中指定某些属性，且排除某些属性转换，转换方式通过风格转换器指定
public static NeoMap from(Object object, NamingChg naming, List<String> inFieldList, List<String> exFieldList) {}

// 将其他NeoMap对应的key通过风格变换器转换过来
public static NeoMap fromMap(NeoMap sourceMap, NamingChg namingChg) {}
```