# 十二、实体代码生成器

这里借鉴mybatis的实体生成器想法，根据jdbc中数据库字段和java类的映射，来生成对应的实体，我们这里有别于mybatis，对一些枚举类型做了特殊处理，对于公共的一些枚举类型，这里及进行抽离了出来。我们首先看下怎么生成实体。

```java
@Test
public void test1(){
  EntityCodeGen codeGen = new EntityCodeGen()
    // 数据库信息
		.setDb("neo_test", "neo@Test123", "jdbc:mysql://127.0.0.1:3306/neo?useUnicode=true&characterEncoding=UTF-8&useSSL=false")
    // 设置项目路径
    .setProjectPath("/Users/xxx/xxx/Neo")
    // 设置实体生成的包路径
    .setEntityPath("com.simonalong.neo.entity")
    // 设置表前缀过滤
    .setPreFix("neo_")
    // 设置要排除的表
    //.setExcludes("xx_test")
    // 设置只要的表
    .setIncludes("neo_table3", "neo_table4")
    // 设置属性中数据库列名字向属性名字的转换，这里设置下划线，比如：data_user_base -> dataUserBase
    .setFieldNamingChg(NamingChg.UNDERLINE);

  // 代码生成
  codeGen.generate();
}
```

<a name="c2762ca4"></a>

<h1 id="生成实体">1.生成实体</h1>

根据上面的配置即可在对应的位置生成对应的实体结构。比如包含所有字段的表如下

```sql
CREATE TABLE `xx_test5` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `group` char(64) COLLATE utf8_unicode_ci NOT NULL DEFAULT '' COMMENT '数据来源组，外键关联lk_config_group',
  `name` varchar(64) COLLATE utf8_unicode_ci NOT NULL DEFAULT '' COMMENT '任务name',
  `user_name` varchar(24) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '修改人名字',
  `gander` enum('Y','N') COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '性别：Y=男；N=女',
  `biginta` bigint(20) NOT NULL,
  `binarya` binary(1) NOT NULL,
  `bit2` bit(1) NOT NULL,
  `blob2` blob NOT NULL,
  `boolean2` tinyint(1) NOT NULL,
  `char1` char(1) COLLATE utf8_unicode_ci NOT NULL,
  `datetime1` datetime NOT NULL,
  `date2` date NOT NULL,
  `decimal1` decimal(10,0) NOT NULL,
  `double1` double NOT NULL,
  `enum1` enum('a','b') COLLATE utf8_unicode_ci NOT NULL,
  `float1` float NOT NULL,
  `geometry` geometry NOT NULL,
  `int2` int(11) NOT NULL,
  `linestring` linestring NOT NULL,
  `longblob` longblob NOT NULL,
  `longtext` longtext COLLATE utf8_unicode_ci NOT NULL,
  `medinumblob` mediumblob NOT NULL,
  `medinumint` mediumint(9) NOT NULL,
  `mediumtext` mediumtext COLLATE utf8_unicode_ci NOT NULL,
  `multilinestring` multilinestring NOT NULL,
  `multipoint` multipoint NOT NULL,
  `mutipolygon` multipolygon NOT NULL,
  `point` point NOT NULL,
  `polygon` polygon NOT NULL,
  `smallint` smallint(6) NOT NULL,
  `text` text COLLATE utf8_unicode_ci NOT NULL,
  `time` time NOT NULL,
  `timestamp` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  `tinyblob` tinyblob NOT NULL,
  `tinyint` tinyint(4) NOT NULL,
  `tinytext` tinytext COLLATE utf8_unicode_ci NOT NULL,
  `text1` text COLLATE utf8_unicode_ci NOT NULL,
  `text1123` text COLLATE utf8_unicode_ci NOT NULL,
  `time1` time NOT NULL,
  `timestamp1` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',
  `tinyblob1` tinyblob NOT NULL,
  `tinyint1` tinyint(4) NOT NULL,
  `tinytext1` tinytext COLLATE utf8_unicode_ci NOT NULL,
  `year2` year(4) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci COMMENT='配置项';
```

生成的结构

```java
import java.sql.Date;
import java.sql.Time;
import java.sql.Timestamp;
import java.math.BigDecimal;
import lombok.Data;

/**
 * 配置项
 * @author robot
 */
@Data
public class Test5DO {

    private Long id;

    /**
     * 数据来源组，外键关联lk_config_group
     */
    private String group;

    /**
     * 任务name
     */
    private String name;

    /**
     * 修改人名字
     */
    private String userName;

    /**
     * 性别：Y=男；N=女
     */
    private String gander;
    private Long biginta;
    private byte[] binarya;
    private Boolean bit2;
    private byte[] blob2;
    private Boolean boolean2;
    private String char1;
    private Timestamp datetime1;
    private Date date2;
    private BigDecimal decimal1;
    private Double double1;
    private String enum1;
    private Float float1;
    private byte[] geometry;
    private Integer int2;
    private byte[] linestring;
    private byte[] longblob;
    private String longtext;
    private byte[] medinumblob;
    private Integer medinumint;
    private String mediumtext;
    private byte[] multilinestring;
    private byte[] multipoint;
    private byte[] mutipolygon;
    private byte[] point;
    private byte[] polygon;
    private Integer smallint;
    private String text;
    private Time time;
    private Timestamp timestamp;
    private byte[] tinyblob;
    private Integer tinyint;
    private String tinytext;
    private String text1;
    private String text1123;
    private Time time1;
    private Timestamp timestamp1;
    private byte[] tinyblob1;
    private Integer tinyint1;
    private String tinytext1;
    private Date year2;
}
```

<a name="186b049c"></a>

<h2 id="抽离公共枚举">2.抽离公共枚举</h2>

对于表中有枚举类型的话，则会先看是否已经有对应的枚举类了，如果有，则不生成，如果有同名的枚举类，但是枚举类型又不同，则会生成内部的枚举类。<br />表1：（关注其中的枚举类型）

```sql
CREATE TABLE `neo_table3` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `group` char(64) COLLATE utf8_unicode_ci NOT NULL DEFAULT '' COMMENT '数据来源组，外键关联lk_config_group',
  `name` varchar(64) COLLATE utf8_unicode_ci NOT NULL DEFAULT '' COMMENT '任务name',
  `user_name` varchar(24) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '修改人名字',
  `age` int(11) DEFAULT NULL,
  `n_id` int(11) unsigned NOT NULL,
  `sort` int(11) NOT NULL,
  `enum1` enum('A','B') COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '类型：A=成功；B=失败',
  PRIMARY KEY (`id`),
  KEY `group_index` (`group`)
) ENGINE=InnoDB AUTO_INCREMENT=34 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
表2：（关注其中的枚举类型）

```sql
CREATE TABLE `neo_table4` (
  `id` int(11) unsigned NOT NULL,
  `group` char(64) COLLATE utf8_unicode_ci NOT NULL DEFAULT '' COMMENT '数据来源组，外键关联lk_config_group',
  `name` varchar(64) COLLATE utf8_unicode_ci NOT NULL DEFAULT '' COMMENT '任务name',
  `user_name` varchar(24) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '修改人名字',
  `age` int(11) DEFAULT NULL,
  `n_id` int(11) unsigned NOT NULL,
  `sort` int(11) NOT NULL,
  `enum1` enum('Y','N') COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '类型：Y=成功；N=失败',
  PRIMARY KEY (`id`),
  KEY `group_index` (`group`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
两个表都是有对应的枚举类型，枚举类型名字相同，则在进行生成的时候，两个一起的话，则先生成的对应的枚举会是公共的，而后者是内部的，如下：

```java
/**
 * 类型
 * @author robot
 */
@Getter
@AllArgsConstructor
public enum Enum1 {

    /**
     * 失败
     */
    B("B"),
    /**
     * 成功
     */
    A("A"),
;

    private String value;
}
```

```java
/**
 * @author robot
 */
@Data
public class Table4DO {

    private Long id;

    /**
     * 数据来源组，外键关联lk_config_group
     */
    private String group;

    /**
     * 任务name
     */
    private String name;

    /**
     * 修改人名字
     */
    private String userName;
    private Integer age;
    private Long nId;
    private Integer sort;

    /**
     * 类型：Y=成功；N=失败
     */
    private String enum1;

    /**
    * 类型
    */
    @Getter
    @AllArgsConstructor
    public enum Enum1 {
        /**
        * 成功
        */
        Y("Y"),
        /**
        * 失败
        */
        N("N"),
        ;
        private String value;
    }
}
```

**注意：**<br />其中对于枚举类型的注释，这里采用解析字段的remark字段，格式为：
> 描述:A=xx;B=xxx

其中描述和后面分隔采用冒号，中文的":"和英文"："的均可，类型之间用分号"；"和";"都可以