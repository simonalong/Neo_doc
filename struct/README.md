# 三、结构信息

除了常见的查询之外，这里还提供了对于库的表结构的查询：
<a name="f8ada07a"></a>

<h3 id="表信息">1.表信息</h3>

查询库中表列表：

```java
public List<String> getAllTableNameList(){}
```

<a name="47061b39"></a>

<h3 id="列信息">2.列信息</h3>

表的所有列名：

```java
public Set<String> getColumnNameList(String tableName){}
public List<NeoColumn> getColumnList(String tableName){}
```

<a name="e46328b9"></a>

<h3 id="索引信息">3.索引信息</h3>

表的所有索引信息：

```java
public List<Index> getIndexList(String tableName){}
public List<String> getIndexNameList(String tableName){}
```

<a name="c21b6bee"></a>

<h3 id="表创建的sql">4.表创建的sql(*)</h3>

表的创建语句：

```java
/**
* 获取创建sql的语句
* {@code
* create table xxx{
*     id xxxx;
* } comment ='xxxx';
* }
*
* @param tableName 表名
* @return 表的创建语句
*/
public String getTableCreate(String tableName){
  return (String) (execute("show create table `" + tableName + "`").get(0).get(0).get("Create Table"));
}
```
该功能是一个很不错的功能，在进行动态化分表的时候，获取原表的创建语句，修改表名，然后再配合execute执行创建表语句，则可以创建新的表，即可以实现动态的分库分表（其中动态化是利用自己开发的分布式配置中心的配置热启动功能，该分布式配置中心请见[这里](https://www.yuque.com/simonalong/xiangmu/pxg5a8)）。
