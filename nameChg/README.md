# 六、命名转换

针对数据库的命名字段风格和系统内部的命名风格不一样，这里增加了风格转换（有借鉴其他框架思想，具体实现不同）。其中风格转换主要就是基于NeoMap进行风格转换，NeoMap默认就是跟DB中的字段保持一致的，而转换到具体的实体中，则需要进行转换

<h2 id="转换风格">1.转换风格</h2>

项目中设定的风格为如下基本这些，而更多的转换，则可以将相互的风格再嵌套即可扩展更多

| 转换类型 | 风格1 | 风格2 |
| --- | --- | --- |
| DEFAULT | 风格不转换 |  |
| BIGCAMEL | 小驼峰（dataBaseUser） | 大驼峰（DataBaseUser） |
| UNDERLINE | 小驼峰（dataBaseUser） | 下划线（data_base_user） |
| PREUNDER | 小驼峰（dataBaseUser） | 前下划线（_data_base_user） |
| POSTUNDER | 小驼峰（dataBaseUser） | 后下划线（data_base_user_） |
| PREPOSTUNDER | 小驼峰（dataBaseUser） | 前后下划线（_data_base_user_） |
| MIDDLELINE | 小驼峰（dataBaseUser） | 中划线（data-base-user） |
| UPPERUNER | 小驼峰（dataBaseUser） | 大写下划线（DATA_BASE_USER） |
| UPPERMIDDLE | 小驼峰（dataBaseUser） | 大写中划线（DATA-BASE-USER） |

<a name="ifeWS"></a>

<h2 id="NeoMap设置">2.NeoMap设置</h2>

对于NeoMap和实体转换时候需要设定风格，可以每次都在函数中添加转换，也可以设定全局转换
<a name="39hoz"></a>

<h4 id="单独设置">单独设置</h4>

在实体entity作为参数的时候，一般情况下后面都会有一个参数是namingChg，这个参数是用于entity向NeoMap转换。<br />**注意：**<br />其中转换函数有两个：一个是风格1向风格2转换，一个是风格2向风格1转换，对于实体向NeoMap则是采用风格1向风格2转换函数，而NeoMap向entity转换也是用到风格1向风格2的函数，只是用该函数获取字段，然后对字段赋值而已，只有特殊情况下才会用到风格2向风格1的转换。这些都不需要使用者关心，只需要知道一下即可。
<a name="L2Phd"></a>

<h4 id="全局设置">全局设置</h4>

如果我们不想每次都那么麻烦的转换，则可以对NeoMap设置全局转换

```java
public static void setDefaultNamingChg(NamingChg namingChg) {}
```

**注意：**<br />一旦设置了，所有的NeoMap到实体的转换都是用这个转换方式了