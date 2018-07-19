---
title: "Java.FunctionInterface"
date: "2018-07-19"
categories:
 - "整理"
tags:
 - "Java"
 - "Java8"
toc: true
---


# Effect
- 用于在 Java 8 中 **支持 Lambda 表达式**
- Java8 中新特性


# Introduce
## Function.Interface
- 接口
- 只有一个抽象方法
    - SAM 接口（Single Abstract Method interfaces）

## Annotation
- `@FunctionalInterface`
    - 用于 **检测编译级错误**
        - 提醒编译器 **检测**接口是否 **有且仅有一个抽象方法**
        - 对是否支持函数式接口 **无影响**

## Situation.allow
- 定义 **默认**方法
- 定义 **静态**方法
- 覆盖实现 **Object.public** 方法
- 声明`可检查异常`


# List
- `java.lang.Runnable`
    - `public abstract void run()`
- `java.util.Comparator`
    - `int compare(T o1, T o2)`
- `java.util.concurrent.Callable`
    - `V call() throws Exception`


# Change
## Interface.Default
- 为 Iterable/ Collection 增加方法
    - 为保障接口的向后兼容，增加了 `default function`


# Example
## Define& Use
```java
- Define
@FunctionalInterface
public interface FInterface {

    // 抽象方法
    void print();

    // 默认方法
    default void printDefault() {
        System.out.println("FInterface.default");
    }

    // 静态方法
    static Object printStatic() {
        System.out.println("FInterface.static");
        return "";
    }

    // 覆盖 Object 的 public 方法
    @Override
    String toString();
}

- Use
public static void main(String[] args) {

    FInterface fi = () -> System.out.println("FInterface.print");
    fi.print();
    fi.printDefault();
    FInterface.printStatic();
    System.out.println(fi);
}
```

## Iterable.default-function

```java
public interface Iterable<T> {

    **default** void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }
}
```


# Reference
- [什么是函数式接口（Functional Interface）](https://www.cnblogs.com/chenpi/p/5890144.html)
- [Java 8函数式接口functional interface的秘密](http://colobu.com/2014/10/28/secrets-of-java-8-functional-interface/#JDK_8%E4%B9%8B%E5%89%8D%E5%B7%B2%E6%9C%89%E7%9A%84%E5%87%BD%E6%95%B0%E5%BC%8F%E6%8E%A5%E5%8F%A3)
- [Java8学习笔记（1） -- 从函数式接口说起](https://blog.csdn.net/zxhoo/article/details/38349011)
- [Java 8 函数式接口](http://www.runoob.com/java/java8-functional-interfaces.html)