---
title: "Java.This"
date: "2020-04-21"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---


## 引言
- 查看 `Cglib` 实现源码时，`ClassLoaderData.this` 的用法很有趣，故此查了资料，深入了解
```java
public ClassLoaderData(ClassLoader classLoader) {
    if (classLoader == null) {
        throw new IllegalArgumentException("classLoader == null is not yet supported");
    } else {
        this.classLoader = new WeakReference(classLoader);
        Function<AbstractClassGenerator, Object> load = new Function<AbstractClassGenerator, Object>() {
            public Object apply(AbstractClassGenerator gen) {
                Class klass = gen.generate(ClassLoaderData.this);
                return gen.wrapCachedClass(klass);
            }
        };
        this.generatedClasses = new LoadingCache(GET_KEY, load);
    }
}
```


## 含义
- `Thread.this`
    在 `Thread` 的内部类中使用，用于指向当前内部类关联的外部类（Thread）的实例
- `Thread.class`
    - 指向 `Thread` 的类，一般用于反射


## 扩展
### 内部类
- 定义
    - 于一个类或方法中，所定义的类
- 注意事项
    - 未使用 `static` 声明的内部的类，不能声明静态初始化方法，不能声明静态变量（除非是常量 `static final`）
        - 内部类，在外部类构建后，实际调用了才会加载（内部类持有外部类的指针，即 `OuterClass.this`，因此需先实例化外部类）
        - 而静态属性方法，在一开始就会尝试加载，但内部类此时未加载，从而导致异常
        - 语义上理解，内部类依赖于外部类，而静态声明则独立于实例存在，两者存在冲突


### 静态和常量属性的读取时机
- 触发时机
    - `clinit`
        - 类加载时的初始化动作
        - 仅当类存在静态变量、块时，于 JVM 第一次加载 class 文件时调用
    - `init`
        - 实例创建时调用
            - `new constructor(...)` 
            - `java.lang.reflect.Constructor.newInstance()`
            - `{instance}.clone()`
            - `java.io.ObjectInputStream.getObject()`

- 示例

```java
/**
 * 静态变量可感知，但需加载以获取实际值
 * <p>
 * 结果：
 * StaticParam.value= 0
 * 100
 */
public class StaticParam {

    static {
        System.out.println(StaticParam.class.getSimpleName() + ".value= " + StaticParam.VALUE);
    }

    static int VALUE = 100;
}

/**
 * 编缀时已知道数值，可直接访问
 * <p>
 * 结果：
 * 100
 */
public class StaticFinalParam {

    static {
        System.out.println(StaticFinalParam.class.getSimpleName() + ".value= " + StaticFinalParam.VALUE);
    }

    static final int VALUE = 100;
}

/**
 * 编译时不知道结果，所以仍需调用一次类加载，进行数据渲染
 * <p>
 * 结果：
 * StaticFinalRandomParam.value= 0
 * 179608991
 */
public class StaticFinalRandomParam {

    static {
        System.out.println(StaticFinalRandomParam.class.getSimpleName() + ".value= " + StaticFinalRandomParam.VALUE);
    }

    static final int VALUE = new Random().nextInt();
}


public class LoadTest {

    @Test
    public void testStaticParam() {

        System.out.println(StaticParam.VALUE);
    }
    @Test
    public void testStaticFinalParam() {

        System.out.println(StaticFinalParam.VALUE);
    }
    @Test
    public void testStaticFinalRandomParam() {

        System.out.println(StaticFinalRandomParam.VALUE);
    }
}
```

### JVM 演进
- 针对类加载、方法属性、静态属性、常量等的加载渲染
    - 此处先不进行展开（主要因为还没理清！！）
    - 不过可参考 [JVM常量池浅析](https://www.jianshu.com/p/cf78e68e3a99)

## 参考
- [8.1.3. Inner Classes and Enclosing Instances](https://docs.oracle.com/javase/specs/jls/se8/html/jls-8.html#jls-8.1.3)
- [15.8.3. this](https://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.8.3)
- [成员内部类里面为什么不能有静态成员和方法？](https://www.cnblogs.com/xh_Blog/p/6496511.html)
- [“init”与“clinit”的区别](https://bestqiang.github.io/2019/05/06/init-%E4%B8%8E-clinit-%E7%9A%84%E5%8C%BA%E5%88%AB/)
- [关键字final static与类加载分析](https://my.oschina.net/xianggao/blog/93232)
- [Java中局部变量、实例变量和静态变量在方法区、栈内存、堆内存中的分配](https://blog.csdn.net/leunging/article/details/80599282)