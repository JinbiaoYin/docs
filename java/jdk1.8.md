# JKD1.8 新特性_尚硅谷

## Lambda表达式
本质是个匿名函数

```java
@Test
public void test(){
    List<User> list = filterUsers(users, (e) -> e.getAge() >= 20);
    list.forEach(System.out::println);
}
```
实际上`(e) -> e.getAge() >= 20`相当于实现了一个接口的一个方法，是个匿名内部类。

箭头操作符左侧为参数列表，右侧为Lambda体。Lambda表达式需要函数式接口的支持。

如果Lambda体中存在多条语句，需要`()-{}`

跟js中的差不多。

jkd1.8 内置了四种基本函数式接口分别为：
```java
Consumer<T>:消费型接口
    void accept(T t);
Supplier<T>:供给型接口
    T get();
Function<T,R>:函数型接口
    R apply(T t);
Predicate<T>:断言型接口
    boolean test(T t);
```
还有一些其它基于这些的接口的子接口，例如:
```java
BiFunction<T,U,R>
UnaryOperator<T>
BinaryOperator<T>
BiConsumer<T,U>
ToIntFunction<T>
...
```

## 方法引用与构造器引用
### 方法引用
`::`
三种使用情景：
- 对象::方法名
- 类::静态方法名
- 类::实例方法名

调用方法的返回值要与接口的返回值保持一致。
```java
/*
 * 对象::方法名
 */
User user = new User();
Consumer<String> con = () -> user.getName();
//相当于
Consumer<String> con1 = user::getName;
```

```java
/*
 * 类::静态方法名
 */
Comparator<Integer> com = (x,y) -> Integer.compare(x,y);
//相当于
Comparator<Integer> com1 = Integer::compare;
```

```java
/*
 * 类::实例方法名
 */
BiPredicate<String,String> bp = (x, y) -> x.equals(y);
//相当于
BiPredicate<String,String> bp = String::equals;
```

### 构造器引用
无参构造
```java
Supplier<Employee> sup = () -> new Employee();
//相当于
Supplier<Employee> sup2 = Employee::new;
```

一个参数的构造器
```java
Function<Integer,Employee> fun = (x) -> new Employee(x);
//相当于
Function<Integer,Employee> fun2 = Employee::new;
```


### 数组引用
```java
Function<Integer, String[]> fun = (x) -> new String[x];
//
Function<Integer, String[]> fun2 = String[]::new;
```