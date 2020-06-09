---
title: mybatis
---

## jdbc Oracle MySQL 对应表

| Mybatis  | JdbcType       | Oracle          | MySQL               |
|----------|----------------|-----------------|---------------------|
| JdbcType | ARRAY          |                 |                     |
| JdbcType | BIGINT         |                 | BIGINT              |
| JdbcType | BINARY         |                 |                     |
| JdbcType | BIT            |                 | BIT                 |
| JdbcType | BLOB           | BLOB            | BLOB                |
| JdbcType | BOOLEAN        |                 |                     |
| JdbcType | CHAR           | CHAR            | CHAR                |
| JdbcType | CLOB           | CLOB            | TEXT                |
| JdbcType | CURSOR         |                 |                     |
| JdbcType | DATE           | DATE            | DATE                |
| JdbcType | DECIMAL        | DECIMAL         | DECIMAL             |
| JdbcType | DOUBLE         | NUMBER          | DOUBLE              |
| JdbcType | FLOAT          | FLOAT           | FLOAT               |
| JdbcType | INTEGER        | INTEGER         | INTEGER             |
| JdbcType | LONGVARBINARY  |                 |                     |
| JdbcType | LONGVARCHAR    | LONG VARCHAR    |                     |
| JdbcType | NCHAR          | NCHAR           |                     |
| JdbcType | NCLOB          | NCLOB           |                     |
| JdbcType | NULL           |                 |                     |
| JdbcType | NUMERIC        | NUMERIC/NUMBER  | NUMERIC/            |
| JdbcType | NVARCHAR       |                 |                     |
| JdbcType | OTHER          |                 |                     |
| JdbcType | REAL           | REAL            | REAL                |
| JdbcType | SMALLINT       | SMALLINT        | SMALLINT            |
| JdbcType | STRUCT         |                 |                     |
| JdbcType | TIME           |                 | TIME                |
| JdbcType | TIMESTAMP      | TIMESTAMP       | TIMESTAMP/DATETIME  |
| JdbcType | TINYINT        |                 | TINYINT             |
| JdbcType | UNDEFINED      |                 |                     |
| JdbcType | VARBINARY      |                 |                     |
| JdbcType | VARCHAR        | VARCHAR         | VARCHAR             |

## resultType和resultMap
`resultType`指定的是一个pojo对象，只有满足以下条件才能映射成功。
- sql语句中要查询的列名和pojo的属性名完全一致，否则参数映射不成功，映射不成功的为null。
- 有全参构造方法，或者是含有查询列的构造方法
- 必须放在 mybatis.type-aliases-package 包下，否则mybatis找不到

`resultMap`用于比较复杂的sql，可以在mapper.xml自定义映射。