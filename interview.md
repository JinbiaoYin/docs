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
### 重载与重写的区别
重载是一个类中以统一方法处理不同数据类型的一种手段。方法名相同，参数个数和类型不能相同。访问权限和返回值可相同也可不相同，无所谓。

重写是子类覆盖父类的方法，访问权限必须大于等于父类方法的权限，返回值类型，方法名，参数的个数和类型必须相同。子类无法抛出更大，更多的异常。


### `Throwable`是Java语言中所有错误或异常的超类。它的子类`Error`和`Exception`有什么区别？
`Error`是指 Java 运行时系统内部错误和系统资源耗尽错误。有堆栈溢出，内存溢出等错误。编译器无法做检查，程序员无法直接做出处理。  
`Exception`分为运行时异常和受检异常，运行时例如空指针异常，类型转换异常。非运行时异常有IO异常，SQL异常等。其中`RuntimeException`是Java虚拟机正常运行时抛出的异常的超类，可以不被声明或者捕获。受检异常通常发生在编译阶段，编译器会强制程序去显式声明或者捕获受检异常。

### `throw`和`throws`的区别
位置不同，`throws`用在方法上，用于声明此方法可能产生的异常；`throw`用于方法内，抛出一个异常对象。

### MySQL中事务的特性？
原子性，一致性，隔离性，持久性。

### MySQL中的隔离级别，默认是哪个隔离级别，分别造成的问题？
- 读未提交。会产生脏读，不可重复读，幻读的问题。
- 读已提交。会产生不可重复读，幻读的问题。
- 可重复读。会产生幻读的问题。
- 串行化。没有以上问题。

可重复读是MySQL的默认隔离级别。

### spring中事务的传播行为？
事务中传播行为有7种，分别为：  
- propagion_required:支持当前事务，如果不存在，就新建一个。（默认的传播行为）。  
- propagion_supports:支持当前事务，如果不存在，就不使用事务。  
- propagion_mandatory:支持当前事务，如果不存在，抛出异常
- propagion_requires_new:如果有事务存在，挂起当前事务，创建一个新的事务。
- propagion_not_supported:以非事务方式运行，如果有事务存在，挂起当前事务。
- propagion_never:以非事务方式运行，如果有事务存在，抛出异常。
- propagion_nested:如果当前事务存在，则嵌套事务执行。

### MySQL中`char`和`varchar`的区别？什么时候用`char`，什么时候用`varchar`？
MySQL中`char`是固定长度的，`varchar`是可变长度的。

`char`会把没撑满的空间用空格补足，因此会浪费一些空间，检索它的时候尾部空格会被去除。

`varchar`长度在 0-255 时，会使用额外1个字节来标识字串长度（0-255使用1个字节，超过255需要2个字节）。

对于长度基本相等的，选用`char`，否则选用`varchar`，当`varchar`长度过大时，使用`text`（阿里巴巴规范是超过5000，就采用`text`）。

### 常用的集合及各个集合的应用场景
- HashMap:
- ConcurrentHashMap
- ArrayList:
- LinkedList:

### 并发安全下想使用List怎么办？
1. 使用`Vector`集合，它的所有方法都是被`synchronized`修饰的，因此效率较低。
2. 使用`Collections.synchronizedList(List list)`来修饰所需要的`List`，例如：
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

`java.util.concurrent.CopyOnWriteArrayList`和`java.util.concurrent.CopyOnWriteArraySet`，读取元素时就不用加锁，写数据时才加锁，大大提升了读取性能。但只适合于读多写少的情况，如果写多读少，使用这个就没意义了，因为每次写操作都要进行集合内存复制，性 能开销很大，如果集合较大，很容易造成内存溢出。

### Get请求和Post请求的区别？
1. GET 把参数包含在URL中，POST通过请求体传递参数，对用户不可见。
2. URL的编码格式采用的是ASCII编码。特定的浏览器和服务器对URL的长度有限制。因此，在使用GET请求时，传输数据会受到URL长度的限制。对于POST，由于不是URL传值，理论上是不会受限制的。
3. POST的安全性比GET的高。这个安全性也只是一个简单来说的安全。也就是说通过GET请求，用户名和密码都会暴露再URL上，通过查看日志，用户名和密码就很容易被他人拿到。

### 如何保持接口的幂等性？
接口幂等性就是用户对于同一操作发起的一次请求或者多次请求的结果是一致的，不会因为多次点击而产生了副作用。
CURD中，查询是天然幂等的，因为我们在查询中不做任何更改状态的操作。  
删除操作也是幂等的，删除一次和多次删除都是把数据删除。  
更新操作，如果是状态的直接赋值更新，而不是在原有状态上自增或自减，那么它也是幂等的。
而添加操作，则可能会由于前端的重复提交，产生重复数据。所以这种接口，可以使用token机制来保持幂等性。在执行业务前，必须去获取一个token，保存在redis中，并返回。然后再根据前端带的token来请求接口，首先删除token，如果删除成功，继续执行业务逻辑。如果删除失败，则token不存在redis中，就表示是重复操作，直接返回重复标记给client，这样就保证了幂等性。

### 什么是缓存击穿？它是怎么发生的？如何解决缓存击穿？
### MySQL 的索引的好处？缺点是什么？一般如何使用？
### `HashMap`的底层结构和扩容机制，线程不安全如何替代？
### `ConcurrentHashMap`是如何实现的？`Segment`是什么？`CAS`是什么？

### 过滤器，拦截器和监听器的区别？它们的作用分别是什么？

### 微服务异常怎么设计？
通常有全局异常拦截器，我们可以设计成系统异常和业务异常。其中系统异常是由于空指针异常，数组越界等异常组成，这些异常被捕获后，抛出系统内部错误异常。而业务层的由于数据字段不能为空，数据过长等业务校验，抛出业务异常。

### 浅拷贝和深拷贝
基本数据类型赋值，实际上是拷贝它的值。而对象引用则是拷贝它的引用。

浅拷贝和深拷贝的区别就是对于引用赋值的区分。

在对对象拷贝时，如果只对基本数据类型进行拷贝，对引用类型只是拷贝引用，没有重新创建对象，则是浅拷贝。反之，是深拷贝。