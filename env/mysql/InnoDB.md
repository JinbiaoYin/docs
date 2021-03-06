# InnoDB 存储引擎

## 简介
MySQL 8.0中，InnoDB是默认的MySQL存储引擎。CREATE TABLE不带ENGINE= 子句的语句将创建一个InnoDB表。

```
CREATE TABLE `biz_user_0` (
  `id` bigint(20) NOT NULL,
  `name` varchar(20) DEFAULT NULL,
  `age` tinyint(2) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

- DDL （Data Definition Language 数据bai定义语言）

```
create table 创建表     
alter table  修改表   
drop table 删除表   
truncate table 删除表中所有行     
create index 创建索引   
drop index  删除索引
```

> DDL语句自动 提交当前 事务；他们无法 回滚。

> delete 和 truncate 的区别: truncate table 一次性删除表中所有数据，删除操作立即生效，不能回滚，不能激活删除触发器，执行速度快。truncate 后，释放的磁盘空间可供系统使用。


- DML（数据操纵语言），一组 SQL执行语句 INSERT， UPDATE和 DELETE操作。该 SELECT声明有时被认为是一个DML语句。

### InnoDB的最佳实践
给使用查询最频繁的一个或多个列为每个表指定一个主键， 如果没有明显的主键，则指定一个 自增主键。这些主键在用于 `where`,`order by`,`group by`和`join`中会自动优化。

### 查看默认存储引擎
```
mysql> SHOW ENGINES;
```

## InnoDB 和 ACID

InnoDB 存储引擎与ACID模型紧密结合，因此数据不会损坏，并且不会因软件崩溃和硬件故障等特殊情况而导致结果失真。 当您依赖符合ACID的功能时，您无需重新发明一致性检查和崩溃恢复机制。

如果有其他软件安全措施，可以容忍少量数据丢失或不一致，可以调整MySQL设置来损失ACID可靠性以获得更高的性能或吞吐量。

### Atomicity 原子性
原子性主要涉及到了 InnoDB 的事务方面。 相关的MySQL功能包括：

- autocommit 设置。
- commit 语句。
- rollback 语句。
- 操纵来自 INFORMATION_SCHEMA 的数据。

### Consistency 一致性
内部 InnoDB 处理以保护数据免于崩溃。 相关的MySQL功能包括：

- InnoDB doublewrite buffer. 双写缓冲
- InnoDB crash recovery. 崩溃恢复

### Isolation 隔离性
隔离 方面主要涉及 InnoDB 事务 ，特别 是适用于每个事务 的 隔离级别 。 相关的MySQL功能包括：

- autocommit 设置。
- 隔离级别的设置。
- InnoDB 的锁。

### Durability 持久性
 持久性 方面涉及MySQL软件功能与您的特定硬件配置交互。 由于取决于CPU，网络和存储设备的功能的许多可能性，这方面是最复杂的。相关的MySQL功能包括：

- InnoDB doublewrite buffer。
- 配置 innodb_flush_log_at_trx_commit 。
- 配置 sync_binlog 。
- innodb_file_per_table 。
- 在存储设备中写入缓冲区，例如磁盘驱动器，SSD或RAID阵列。
- 存储设备中的电池备份缓存。

## InnoDB 多版本控制
InnoDB 内部有一个称为`回滚段`的数据结构，存储在表空间中，用于支持并发和回滚的事务功能。当事务回滚时，使用回滚段中的信息来执行所需的撤销和构建行的早期版本。

具体是怎么实现的呢？

InnoDB 向数据库中存储的每一行都添加了 3 个字段。隐含ID，事务ID（DB_TRX_ID），回滚指针（DB_ROLL_PT）。

- 隐含ID : 插入新行时单调递增。
- 事务ID ：事务版本号，在数据更新时发生变化。
- 回滚指针：指向 undo logs 中更改前的初始内容。

查询时能查到什么内容，依赖于隔离级别。

- 读未提交时，直接读取主记录，无论更新事务是否完成。
- 读已提交，每个查询语句都会创建Read View，因此一次事务中，两个Read View不同，对同一记录的两次读可能不同（幻读）。
- 重复读，事务开始前创建 Read View，每次查询从Read View中查询。
- 串行化。锁表，读写相互阻塞。

> Read View 是事务开启时当前所有事务的一个集合,这个类中存储了当前Read View中最大事务ID及最小事务ID。包含当前所有的活动事务。

undo logs 什么时候删除？
上述我们知道，MVCC 是根据回滚段来实现的，而回滚段是保存在 undo logs 中的，多个事务对同一条记录进行修改，会导致 undo logs 迅速膨胀，虽然 select 操作不再需要加锁了，但是 undo logs的过大也是一个问题。这就提出了一个 purge 线程。

## 参考资料
[MVCC多版本控制详解](https://blog.csdn.net/chen77716/article/details/6742128?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)