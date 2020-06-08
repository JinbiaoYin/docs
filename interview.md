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
- MySQL中事务的特性？  
原子性，一致性，隔离性，持久性。

- MySQL中的隔离级别，默认是哪个隔离级别，分别造成的问题？
  - 读未提交。会产生脏读，不可重复读，幻读的问题。
  - 读已提交。会产生不可重复读，幻读的问题。
  - 可重复读。会产生幻读的问题。
  - 串行化。没有以上问题。
可重复读是MySQL的默认隔离级别。

- spring中事务的传播行为？
    事务中传播行为有7种，分别为：
  - propagion_required:支持当前事务，如果不存在，就新建一个。（默认的传播行为）。  
  - propagion_supports:支持当前事务，如果不存在，就不使用事务。  
  - propagion_mandatory:支持当前事务，如果不存在，抛出异常
  - propagion_requires_new:如果有事务存在，挂起当前事务，创建一个新的事务。
  - propagion_not_supported:以非事务方式运行，如果有事务存在，挂起当前事务。
  - propagion_never:以非事务方式运行，如果有事务存在，抛出异常。
  - propagion_nested:如果当前事务存在，则嵌套事务执行。

- MySQL中`char`和`varchar`的区别？什么时候用`char`，什么时候用`varchar`？
MySQL中`char`是固定长度的，`varchar`是可变长度的。

`char`会把没撑满的空间用空格补足，因此会浪费一些空间，检索它的时候尾部空格会被去除。

`varchar`长度在 0-255 时，会使用额外1个字节来标识字串长度（0-255使用1个字节，超过255需要2个字节）。

对于长度基本相等的，选用`char`，否则选用`varchar`，当`varchar`长度过大时，使用`text`（阿里巴巴规范是超过5000，就采用`text`）。

- 常用的集合及各个集合的应用场景。
- HashMap:
- ConcurrentHashMap
- ArrayList:
- LinkedList:

- 并发安全下想使用List怎么办？
- 使用`Vector`集合，它的所有方法都是被`synchronized`修饰的，因此效率较低。
- 使用`Collections.synchronizedList(List list)`来修饰所需要的`List`，例如：

```java
List<String> list = Collections.synchronizedList(new ArrayList<String>());
list.add("1");
list.add("2");
list.add("3");
synchronized (list) {
    Iterator i = list.iterator(); // Must be in synchronized block
    while (i.hasNext()) {
        //foo(i.next());
        System.out.println(i.next());
    }
}
```
`add()`不需要加`synchronized`，遍历时需要`synchronized`。在读多写少的情况下效率较低。
- `java.util.concurrent.CopyOnWriteArrayList`和`java.util.concurrent.CopyOnWriteArraySet`，读取元素时就不用加锁，写数据时才加锁，大大提升了读取性能。但只适合于读多写少的情况，如果写多读少，使用这个就没意义了，因为每次写操作都要进行集合内存复制，性能开销很大，如果集合较大，很容易造成内存溢出。

- 什么是缓存击穿？它是怎么发生的？如何解决缓存击穿？
- MySQL 的索引的好处？缺点是什么？一般如何使用？
- `HashMap`的底层结构和扩容机制，线程不安全如何替代？
- `ConcurrentHashMap`是如何实现的？`Segment`是什么？`CAS`是什么？