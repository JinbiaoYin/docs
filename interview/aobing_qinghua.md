# B站敖丙面试两年北大

1. 常用的集合及各个集合的应用场景。
- HashMap:
- ConcurrentHashMap
- ArrayList:
- LinkedList:
2. 并发安全下想使用List怎么办？
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

3. `HashMap`的底层结构和扩容机制，线程不安全如何替代？

4. `ConcurrentHashMap`是如何实现的？`Segment`是什么？`CAS`是什么？
