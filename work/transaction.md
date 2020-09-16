# @Transactional

注意常用的该注解有两个，一个是spring的`org.springframework.transaction.annotation.Transactional`和jdk的`javax.transaction.Transactional`。

这两个在使用上基本没什么区别，只是属性名称稍有不同。


以spring的注解使用为例：

## DEMO1
```java
public User createUser(User user) {
    userRepository.save(user);
    throw new RuntimeException();
}
```

正常插入数据。

## DEMO2
```java
@Transactional(rollbackOn = Exception.class)
public User createUser(User user) {
    userRepository.save(user);
    throw new RuntimeException();
}
```

发生回滚，未插入。

## DEMO3
```java
@Transactional
public User createUser(User user) {
    userRepository.save(user);
    throw new RuntimeException();
}
```

发生回滚，未插入。

## DEMO4
```java
@Transactional
public User createUser(User user) throws Exception {
    userRepository.save(user);
    throw new Excepton();
}
```

正常插入数据。由于`@Transactional`回滚默认的设置为`RuntimeException`和`Error`。所以对于父类`Exception`不会发生回滚。


## DEMO5
```java
@Transactional(rollbackOn = Exception.class)
public User createUser(User user) throws Exception {
    userRepository.save(user);
    throw new Exception();
}
```

发生回滚，未插入。