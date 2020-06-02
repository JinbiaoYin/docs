---
title: 面试宝典
---

## 技能树
- Java 编程思想
- MySQL
- Redis
- Spring
- SpringBoot
- SpringCloud Netflix
- MyBatis
- JPA
- Nginx
- Git
- Spring Security Oauth2
- Elastic Search
- Docker
- Maven

## 常见面试题
1. MySQL中事务的特性？  
原子性，一致性，隔离性，持久性。

2. MySQL中的隔离级别，默认是哪个隔离级别，分别造成的问题？
- 读未提交。会产生脏读，不可重复读，幻读的问题。
- 读已提交。会产生不可重复读，幻读的问题。
- 可重复读。会产生幻读的问题。
- 串行化。没有以上问题。
可重复读是MySQL的默认隔离级别。

3. spring中事务的传播行为？
事务中传播行为有7种，分别为：
1. propagion_required:支持当前事务，如果不存在，就新建一个。（默认的传播行为）。  
2. propagion_supports:支持当前事务，如果不存在，就不使用事务。  
3. propagion_mandatory:支持当前事务，如果不存在，抛出异常

4. propagion_requires_new:如果有事务存在，挂起当前事务，创建一个新的事务。
5. propagion_not_supported:以非事务方式运行，如果有事务存在，挂起当前事务。
6. propagion_never:以非事务方式运行，如果有事务存在，抛出异常。
7. propagion_nested:如果当前事务存在，则嵌套事务执行。


- 什么是缓存击穿？它是怎么发生的？如何解决缓存击穿？
- MySQL 的索引的好处？缺点是什么？一般如何使用？