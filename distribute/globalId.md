# 全局id

对于分布式的全局id，这里并没有采用常见的雪花算法，而是在当前版本用到了全部的64big位，数据持久化部分是基于数据库，内存部分采用双buffer的方式进行数据刷新。对外仅提供两个参数进行buffer刷新速度和buffer大小调整。<br />默认情况下是不启用全局id的，如果要启用全局，则需要先进行开启，并设置buffer刷新比率和大小。开启后，则会在对应的库中创建一个全局id表：neo_id_generator。

```java
/**
 * 开启全局id生成器，默认buffer大小是10000，刷新比率是0.2
 */
public void openUidGenerator(){}
public void openUidGenerator(Integer stepSize, Float refreshRatio){}
```

| type | 说明 |
| --- | --- |
| stepSize | 双buffer，其中每一个内存buffer的大小 |
| refreshRatio | 刷新二级buffer的比率，在第一级buffer达到这个比率之后，就派一个线程异步获取数据，填充第二buffer |

<a name="flupG"></a>

<h4 id="表结构">表结构</h4>

```sql
CREATE TABLE `neo_id_generator` (
  `id` int(11) NOT NULL,
  `uuid` bigint(20) NOT NULL DEFAULT '0',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

<a name="IN6DT"></a>

<h4 id="用法">用法</h4>

直接调用如下diamante即可获取分布式全局唯一id

```java
public Long getUid() {}
```