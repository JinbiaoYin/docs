# 马士兵java高并发编程系列合集
## 高并发编程1
### 启动线程的三种方式
1. 继承`Thread`类，重写`run()`方法。  
2. 实现`Runnable`接口的`run()`方法。  
3. 从`Executors`线程池中获取。  


### 常见方法的含义
1. `Thread.sleep(1000);`当前线程休眠 1s，这 1s 内该线程不会继续执行。
2. `Thread.yeild();`当前线程回到就绪状态，线程调度器重新分配资源。
3. `t1.join();`在当前线程中加入 t1 ，当 t1 执行完毕后再继续执行当前线程。

### syschronized 关键字
```java
public class T{
    private int count = 0;
    private Object o = new Object();

    public void m(){
        synchronized(o){//任何线程要执行下面的代码，必须拿到o的锁
            count--;
            System.out.println(Thread.currentThread().getName() + " count = " + count);
        }
    }
}
```

可以用`this`锁
```java
public class T{
    private int count = 0;

    public void m(){
        synchronized(this){//任何线程要执行下面的代码，必须拿到o的锁
            count--;
            System.out.println(Thread.currentThread().getName() + " count = " + count);
        }
    }
}
```

上述等同于直接用`synchronized`修饰方法:
```java
public class T{
    private int count = 0;

    public synchronized void m(){
        count--;
        System.out.println(Thread.currentThread().getName() + " count = " + count);
    }
}
```

`static`方法用`synchronized`修饰
```java
public class T{
    private static int count = 10;
    public synchronized static void m(){//相当于synchronized(T.class)
        count--;
        System.out.println(Thread.currentThread().getName() + " count = " + count);
    }
    public static void mm(){
        synchronized(T.class){
            count--;
        }
    }
}
```
`synchronized`是可重入锁。
