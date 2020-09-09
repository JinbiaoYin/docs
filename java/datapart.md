# 分库分表

## 介绍

- 垂直拆分：根据不同的业务拆分成不同的数据库。

    优点：拆分后业务清晰，拆分规则明确，系统之间整合或扩展更加容易。

    缺点：部分业务表无法 join，跨库查询比较繁琐，会产生分布式事务的问题，提高了系统复杂度。

- 水平拆分：把同一张表的数据拆分到不同数据库中进行存储，或者把一张表拆分成 n 多张小表。按照数据行的拆分。

> 垂直分片可以缓解数据量和访问量带来的问题，但无法根治。如果垂直拆分之后，表中的数据量依然超过单节点所能承载的阈值，则需要水平分片来进一步处理。

> 水平分片又称为横向拆分。它不再将数据根据业务逻辑分类，而是通过某个字段（或某几个字段），根据某种规则将数据分散至多个库或表中，每个分片仅包含数据的一部分。水平分片从理论上突破了单机数据量处理的瓶颈，并且扩展相对自由，是分库分表的标准解决方案。

## 主从复制
主从复制一般是为了解决数据库单点故障，和读写分离的一种有效的解决方案。MySQL 本身就支持主从复制，它是通过`binlog`日志来实现的。

从库生成两个线程，一个I/O线程，一个SQL线程；i/o线程去请求主库的binlog，并将得到的binlog日志写到relay log（中继日志） 文件中；主库会生成一个 log dump 线程，用来给从库 i/o线程传binlog；SQL 线程，会读取relay log文件中的日志，并解析成具体操作，来实现主从的操作一致，而最终数据一致；

具体做法如下：通过 MySQL 中对主节点和从节点的配置，并在从节点中指定主节点的信息即可。

> 主服务器的 my.ini

```
[mysqld]
# 开启日志
log-bin = mysql-bin
binlog_format=ROW
# 设置服务id，主从不能一致
server-id = 1
# 设置需要同步的数据库
binlog-do-db = barcode
# 屏蔽系统库同步
binlog-ignore-db=mysql
binlog-ignore-db=information_schema
binlog-ignore-db=performance_schema
```

> 从库 my.ini

```
[mysqld]
# 开启日志
log-bin = mysql-bin
binlog_format=ROW
# 设置服务id，主从不能一致
server-id = 2
# 设置需要同步的数据库
replicate_wild_do_table=barcode.%
# 屏蔽系统库同步
replicate_wild_ignore_table=mysql.%
replicate_wild_ignore_table=information_schema.%
replicate_wild_ignore_table=performance_schema.%
```

重启服务。主从复制需要主库下创建一个专用账号，它具有主从复制权限。
```
# 授权主备复制专用账号
GRANT REPLICATION SLAVE ON *.* TO 'db_sync'@'%' IDENTIFIED BY 'db_sync';
# 刷新权限
FLUSH PRIVILEGES;
# 确认位点，记录下文件名以及位点
show master status;
```

从库下，需要配置主库的地址信息。
```
# 先停止同步
STOP SLAVE;

# 修改从库指向到主库，使用上一步记录的文件名以及位点
CHANGE MASTER TO
master_host = '192.168.3.187',
master_user = 'db_sync',
master_password = 'db_sync',
master_log_file = 'mysql-bin.000002',
master_log_pos = 154;

# 启动同步
START SLAVE;

# 查看从库状态`Slave_IO_Runing`和`Slave_SQL_Runing`都为 Yes 说明同步成功，如果不为 Yes，请检查 error_log，然后排查相关异常。
show slave status;

# 注意：如果之前此从库已有主库指向，需要先清空
STOP SLAVE IO_THREAD FOR CHANNEL '';
reset slave all;
```

## Apache Shardingsphere
Apache ShardingSphere 是一套开源的分布式数据库中间件解决方案组成的生态圈，它由 JDBC、Proxy 和 Sidecar（规划中）这 3 款相互独立，却又能够混合部署配合使用的产品组成。 它们均提供标准化的数据分片、分布式事务和数据库治理功能，可适用于如 Java 同构、异构语言、云原生等各种多样化的应用场景。

### 公共表
分库中为了避免跨库 join，一般采用公共表的形式，例如数据字典表等。

这种情况下，我们在每个库中都创建这样一张表。

通过在 `application.yml` 配置公共表名即可。

### ShardingSphere-JDBC
采用的是增强JDBC的方式，SJDBC同时连接多个数据源，根据配置对逻辑SQL解析处理产生实际SQL语句。有逻辑表和物理表的概念，写代码时，还是跟从前一样，要针对逻辑表来写SQL语句，SJDBC会将它解析成实际的多个分表SQL，到相应的物理库和物理表中执行并存储。

这种方式的主要工作在于`配置多数据源`和`配置分片规则`。其中配置规则采用的是`groovy`的语法，这里要注意。

> 下面给出了示例，是根据参考DEMO改写的。下面的分片规则是：十位数确定库，个位数确定表。其中十位数为偶数到 ds0 库，奇数到 ds1 库。个位数为偶数到 0 表，奇数到 1 表。麻烦的点在于这个分片规则，需要去熟悉 groovy 语法。

> POM 依赖，主要注意 jdbc 驱动版本，和引入依赖 `sharding-jdbc-spring-boot-starter`。其它根据自己项目正常按需引入。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>apache-shardingsphere</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>sharding-jdbc</artifactId>
    <packaging>jar</packaging>

    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>

    <dependencies>
        <!-- Spring Boot -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- 数据库相关 -->
        <dependency>
            <groupId>com.zaxxer</groupId>
            <artifactId>HikariCP</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.apache.tomcat</groupId>
                    <artifactId>tomcat-jdbc</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <!-- MySQL 驱动的版本号必须是 5.1.48 -->
            <version>5.1.48</version>
        </dependency>
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
        </dependency>

        <!-- Apache ShardingSphere -->
        <dependency>
            <groupId>org.apache.shardingsphere</groupId>
            <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
            <version>4.0.0-RC3</version>
        </dependency>
        
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.apache.shardingsphere.ShardingSphereApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

```yml
spring:
  main:
    allow-bean-definition-overriding: true
  application:
    name: sharding-jdbc
  shardingsphere:
    props:
      sql:
        show: true
# 数据源配置
    datasource:
      names: ds0,ds1
      ds0:
        type: com.zaxxer.hikari.HikariDataSource
        driver-class-name: com.mysql.jdbc.Driver
        jdbc-url: jdbc:mysql://rm-m5ed8483kh091fs9n7o.mysql.rds.aliyuncs.com:3306/barcode_0?useUnicode=true&characterEncoding=utf-8&useSSL=false
        username: yinjinbiao
        password: 'yinjinbiao!2020'
        hikari:
          minimum-idle: 5
          idle-timeout: 600000
          maximum-pool-size: 10
          auto-commit: true
          pool-name: MyHikariCP
          max-lifetime: 1800000
          connection-timeout: 30000
          connection-test-query: SELECT 1
      ds1:
        type: com.zaxxer.hikari.HikariDataSource
        driver-class-name: com.mysql.jdbc.Driver
        jdbc-url: jdbc:mysql://192.168.3.53:3306/barcode_1?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=UTC
        username: root
        password: '123456'
        hikari:
          minimum-idle: 5
          idle-timeout: 600000
          maximum-pool-size: 10
          auto-commit: true
          pool-name: MyHikariCP
          max-lifetime: 1800000
          connection-timeout: 30000
          connection-test-query: SELECT 1
# 数据分片规则配置
    sharding:
# 配置公共表    
      broadcast-tables: sys_dic
# 需要分片的逻辑表名称
      binding-tables: biz_user
# 设置分库策略
      default-database-strategy:
        inline:
# 分片算法表达式，须符合 groovy 语法
# 此处根据 id 分片
# 如果 id 的倒数第二位 为偶数则落入偶数库即 ds0 匹配的数据源
# 如果 id 的倒数第一位 为奇数则落入奇数库即 ds1 匹配的数据源
          algorithm-expression: ds$->{ (((id-id%10)/2) as int) % 2}
# 根据 id 进行分库
          sharding-column: id
# 设置分表策略
      tables:
# 逻辑表名称
        biz_user:
          actual-data-nodes: ds$->{0..1}.biz_user_$->{0..1}
          table-strategy:
            inline:
              algorithm-expression: biz_user_$->{id % 2}
              sharding-column: id
# 可配置多个
      #  tb_order_item:
      #    actual-data-nodes: ds$->{0..1}.tb_order_item_$->{0..1}
      #    table-strategy:
      #      inline:
      #        algorithm-expression: tb_order_item_$->{order_id % 2}
      #        sharding-column: order_id

mybatis:
  type-aliases-package: com.funtl.apache.shardingsphere.domain
  mapper-locations: classpath:mapper/*.xml
````

## 参考资料
- [Apache ShardingSphere 官方文档](https://shardingsphere.apache.org/document/current/cn/overview/#shardingsphere-jdbc)
- [GitHub ShardingSphere-JDBC 参考案例](https://github.com/topsale/spring-boot-samples)

