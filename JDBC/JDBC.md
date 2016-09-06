#JDBC

JDBC，Java Database Connectivity，Java数据库链接。封装多种数据库，提供统一的访问接口。


##数据库

关于数据库的注意点

* 考试时，__建库脚本一定要放到提交目录里面或者放到项目里面__
* __关系也是可以有属性的__
* ER图 - 数据库之间的映射关系

    * 实体：表
    * 属性：列
    * 一对多：建立外键
    * 多对多：建立关系表

* 事务特点

    1. 自动提交关闭
    2. 统一提交
    3. 有问题回滚

* 存储过程，索引，函数，触发器
* 注意函数返回值

##JDBC使用

1. 加入mysql驱动包

    Project右键 -> Properties -> Java Build Path -> Libraries -> Add External JARs

2. 执行三大步

    ``` java
    Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb", "root", "root");
    ```
    
    ``` java
    Statement statement = conn.createStatement();
    ```
    
    ``` java
    ResultSet rs = statement.executeQuery("SELECT * from test");
    ```
    
注意：

* ```rs.wasNull()```：判断是否为空
* ```insert into aaa values(?, ?)```，```?```是占位符。

##事务

``` java
Statement st = conn.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE, 
    ResultSet.CONCUR_UPDATABLE);
```

创建Statement时，可以选择：

* ```TYPE_SCROLL_INSENSITIVE```：可以回滚，不实时敏感，操作对象是副本
* ```TYPE_SCROLL_SENSITIVE```：可以回滚，实时敏感，操作映射

##时间日期

```date(getDate)```, ```time(getTime)```, ```datetime(getDate, getTime)```, ```timestamp(getTimestamp)```
