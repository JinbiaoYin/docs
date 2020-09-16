# Java数组协变与泛型不变性

## 介绍
假设存在以下类：
```java
class Soup<T>{
    public void add(T t){}
}

class Vegetable{

}

class Carrot extends Vegetable{

}
```
- 如果`Soup<Carrot>`是`Soup<Vegetable>`的子类，则称泛型`Soup<T>`是协变的。
- 如果`Soup<Carrot>`和`Soup<Vegetable>`无关，则称泛型`Soup<T>`是不变的。
- 如果`Soup<Carrot>`是`Soup<Vegetable>`的父类，则称泛型`Soup<T>`是逆变的。

## 泛型的不变性
```java
Soup<Vegetable> soup = new Soup<Carrot>();//编译器错误
soup.add(new Tomato());
```
泛型具有不变性，`Soup<Carrot>`和`Soup<Vegetable>`无关。上述编译器错误。

## 数组的协变性
```java
Vegetable[] vegetables = new Carrot[10];
vegetables[0] = new Tomato();//运行时错误
```
因为数组是协变的，编译器允许把Carrot[10]赋值给Vegetable[]类型的变量，所以这段代码可以顺利通过编译。只有在运行期，JVM真的试图往一堆胡萝卜中插入一个西红柿的时候，才发现大事不好。所以，上面的代码在运行期会抛出一个java.lang.`ArrayStoreException`类型的异常。

## 当泛型也想要协变和逆变
协变
```java
public void drink(Soup<? extends Vegetable> soup) {}
```

逆变
```java
public void drink(Soup<? super Vegetable> soup) {}
```