# 删除

删除这里也有多种处理

```java
public Integer delete(String tableName, Long id) {}
public Integer delete(String tableName, NeoMap searchMap) {}
public <T> Integer delete(String tableName, T entity) {}
public <T> Integer delete(String tableName, T entity, NamingChg naming) {}
```

| 参数 | 类型 | 详解 |
| --- | --- | --- |
| tableName | String | 表名 |
| id | Long | 表的主键列 |
| searchMap | NeoMap | 搜索条件 |
| namingChg | NamingChg | 待插入的数据，通过该字符转换可以跟数据库字段对应上 |
