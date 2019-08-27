# 一、数据库连接


针对数据库连接，这里支持多种连接方式
<a name="ZGBXc"></a>

## 1.账号密码链接：

连接也是支持普通的密码账户连接

```java
String url = "jdbc:mysql://127.0.0.1:3306/neo?useUnicode=true&characterEncoding=UTF-8&useSSL=false";
String user = "xxx";
String password = "xxx";
Neo neo = Neo.connect(url, user, password);
```

<a name="KQWul"></a>
## 2.配置路径连接
其中路径是class的配置文件路径

```
Neo neo = Neo.connect("/config/db.properties");
```
其中配置文件可以有如下这么几种：

```
dataSourceClassName=com.mysql.jdbc.jdbc2.optional.MysqlDataSource
dataSource.user=xxx
dataSource.password=xxx
dataSource.databaseName=neo
dataSource.portNumber=3306
dataSource.serverName=127.0.0.1
```

```
jdbcUrl=jdbc:mysql://127.0.0.1:3306/neo?useUnicode=true&characterEncoding=UTF-8&useSSL=false
dataSource.user=xxx
dataSource.password=xxx
```

```
jdbcUrl=jdbc:mysql://127.0.0.1:3306/neo?useUnicode=true&characterEncoding=UTF-8&useSSL=false
username=xxx
password=xxx
autoCommit=true
poolName=Neo
maximumPoolSize=20
idleTimeout=180000
maxLifetime=1800000
connectionTimeout=30000
connectionTestQuery=select 1
minimumIdle=10
```

<a name="IffMd"></a>
<h3 id="Datasource连接">3.Datasource连接：</h3>
也支持Datasource的连接方式

```
Neo neo = Neo.connect(dataSource);
```
