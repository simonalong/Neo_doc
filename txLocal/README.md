# 八、单机事务

针对单机事务，这里一些特性借鉴了spring的`@Transactional`注解，事务有很多特性：只读性，隔离性，传播性，我们这里只暴露只读和隔离，其中传播性默认为如果事务不存在，则创建，否则加入到已经存在的事务中。针对事务，这里增加这么个函数（函数名有借鉴）

```java
public void tx(Runnable runnable) {}
public <T> T tx(Supplier<T> supplier) {}
public <T> T tx(Boolean readOnly, Supplier<T> supplier) {}
public void tx(Boolean readOnly, Runnable runnable) {}
public void tx(TxIsolationEnum isolationEnum, Runnable runnable) {}
public <T> T tx(TxIsolationEnum isolationEnum, Supplier<T> supplier) {}
public void tx(TxIsolationEnum isolationEnum, Boolean readOnly, Runnable runnable) {}
public <T> T tx(TxIsolationEnum isolationEnum, Boolean readOnly, Supplier<T> supplier){}
```

| 参数 | 类型 | 详解 |
| --- | --- | --- |
| runnable | Runnable | 无返回值的执行 |
| supplier | Supplier<T> | 有返回值类型T的事务执行 |
| isolationEnum | TxIsolationEnum | 隔离级别枚举：读未提交，读提交，可重复读，序列化 |
| readOnly | Boolean | true：表示该事务是只读事务，false：表示为普通事务 |

<a name="eae7edbd"></a>

<h3 id="事务只读">1.事务只读</h3>

对于事务中有多个读操作的这种，我们可以对事务添加只读，那么为什么添加只读事务，网上说了很多是在一个事务中有多个读操作，为了前后读取的一致性，才添加只读事务的。我的理解不是这样，我觉得mysql事务的默认隔离级别是不可重复，其实就是在一个事务中，前后读取就是保持一致的。那么什么时候添加只读呢，由于在事务中，根据事务中的读写，增加了各种隔离级别，随着级别越来越高，则耗费的数据库资源也越多，很多都是根据写操作设置的，但是如果我们事务中只有读，则没有必要再耗费那么多资源，因此可以开启只读按钮，用于优化事务。<br />**示例：**

```java
/**
* 只读事务
*/
@Test
public void test5(){
  AtomicReference<List<String>> groupList = new AtomicReference<>();
  AtomicReference<List<String>> nameList = new AtomicReference<>();
  neo.tx(true, ()->{
    groupList.set(neo.values(TABLE_NAME, "group"));
    nameList.set(neo.values(TABLE_NAME, "name"));
  });

  // [12, group555]
  show(groupList);
  // [name333]
  show(nameList);
}
```

<a name="8ff349b1"></a>

<h3 id="事务隔离">2.事务隔离</h3>

事务的隔离级别，这里设置了枚举添加了自定义的枚举

```java
@Getter
@AllArgsConstructor
public enum TxIsolationEnum {

    /**
     * 一个常量，指示防止脏读;可以发生不可重复的读取和幻像读取
     */
    TX_R_U(Connection.TRANSACTION_READ_COMMITTED),
    /**
     * 一个常量，表示可以进行脏读，不可重复读和幻像读
     */
    TX_R_C(Connection.TRANSACTION_READ_COMMITTED),
    /**
     * 一个常量，表示禁止脏读和不可重复读;可以发生幻像读取
     */
    TX_R_R(Connection.TRANSACTION_REPEATABLE_READ),
    /**
     * 一个常量，表示防止脏读，不可重复读和幻像读
     */
    TX_SE(Connection.TRANSACTION_SERIALIZABLE);

    private int level;
}
```

**示例：**

```java
/**
 * 事务的隔离级别
 */
@Test
public void test6(){
  // {age=2, group=kk, id=11, name=name333}
  show(neo.tx(TxIsolationEnum.TX_R_R, ()->{
    neo.update(TABLE_NAME, NeoMap.of("group", "kk"), NeoMap.of("id", 11));
    return neo.one(TABLE_NAME, NeoMap.of("id", 11));
  }));
}
```