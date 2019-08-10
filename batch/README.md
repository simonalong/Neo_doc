# 四、批量功能

对于批量功能，这里提供了批量更新和批量插入

<h3 id="批量插入">1.批量插入</h3>

```java
public Integer batchInsert(String tableName, List<NeoMap> dataMapList) {}
public <T> Integer batchInsertEntity(String tableName, List<T> dataList){}
public <T> Integer batchInsertEntity(String tableName, List<T> dataList, NamingChg namingChg){}
```

| 参数 | 类型 | 详解 |
| --- | --- | --- |
| tableName | String | 表名 |
| dataMapList | List<NeoMap> | 要插入的数据 |
| dataList | List<T> | 要插入的数据，实体化列表形式 |
| namingChg | NamingChg | 实体跟NeoMap（或者叫跟DB）字段映射 |

<a name="c0d24f84"></a>

<h3 id="批量更新">2.批量更新</h3>

```java
public Integer batchUpdate(String tableName, List<NeoMap> dataList){}
public Integer batchUpdate(String tableName, List<NeoMap> dataList, Columns columns){}
public <T> Integer batchUpdateEntity(String tableName, List<T> dataList){}
public <T> Integer batchUpdateEntity(String tableName, List<T> dataList, Columns columns, NamingChg namingChg){}
public <T> Integer batchUpdateEntity(String tableName, List<T> dataList, Columns columns){}
```

| 参数 | 类型 | 详解 |
| --- | --- | --- |
| tableName | String | 表名 |
| dataMapList | List<NeoMap> | 要插入的数据 |
| dataList | List<T> | 要插入的数据，实体化列表形式 |
| namingChg | NamingChg | 实体跟NeoMap（或者叫跟DB）字段映射 |

提示：<br />上面的批量更新和批量插入，在NeoTable中也都是支持的，少了一个tableName参数，使用起来会更加方便
