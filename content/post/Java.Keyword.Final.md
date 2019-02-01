---
title: "Java.Keyword.Final"
date: "2019-01-29"
categories:
 - "整理"
tags:
 - "Java"
 - "Keyword"
toc: true
---


# 作用
- 用于声明类、方法、变量名，不允许修改其引用

    - 示例

        | 修饰对象 | 作用                                                                                                           |
        |---------|--------------------------------------------------------------------------------------------------------------|
        | 变量     | 只读<br>随类定义存储于方法区(老生代)<br>基本数据类型将不可再次赋值<br>引用类型，将不可指向其它对象              |
        | 方法     | 不允许被子类重写<br>运行更快，编译时已静态绑定，不需在运行时再动态绑定<br> private 方法将被隐式指定为 final 方法 |
        | 类       | 不允许被继承<br> final 类的所有方法将被隐式指定为 final 方法<br>慎重使用                                       |

    - 优势
        - 缓存 final 定义变量，提高性能
        - JVM 对方法、变量和类有一定优化
        - 对多线程共享，减少同步开销

- 多线程场景优化
    - final 可保证创建中的对象不能被其它线程访问
        - final 域，保证当构建函数退出时，其值对其它线程可见
            - JVM会在初始化final变量后插入一个sfence
        - 对象创建分为内存分配和各属性初始化，而初始化时间不可控，导致其它线程有可能访问到一个无效或创建中的对象

# 对比

## 不可变类
- 对象一旦衩创建，便不可再被更改
- 如 String
- 对象只读，多线程下可安全共享


# 示例

- 常量/变量相加

    - 代码

        ```java
        String a = "hello2"; 
        final String b = "hello";
        String d = "hello";
        String c = b + 2;                   // 常量相加，数值也存储于常量池中
        String e = d + 2;                   // 对象+常量，生成新的常量值
        System.out.println((a == c));       // true
        System.out.println((a == e));       // false
        ```

    - 反编译

        ```java
        public static void main(java.lang.String[]);
        Code:
            0: ldc           #2                  // String hello2
            2: astore_1
            3: ldc           #2                  // String hello2
            5: astore_3
            6: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
            9: aload_1
            10: aload_3
            11: if_acmpne     18
            14: iconst_1
            15: goto          19
            18: iconst_0
            19: invokevirtual #4                  // Method java/io/PrintStream.println:(Z)V
            22: return
        ```


# 补充

## 匿名类或接口中所声明的变量，均须以 final 修饰

- `str1+ str2+ str3`

    ```java
    new StringBuilder(
        str1
    )
    .append(
        str2
    )
    .append(
        str3
    ).toString()
    ```

- `str1.concat(str2).concat(str3)`

    ```java
    new String(
        array.copy(
            new String(
                array.copy(
                    str1, 
                    str2)),
            str3))
    ```

- `new StringBuffer(str).append(str2).append(str3).toString()`

    ```java
    new String(
        array.copy(str1, str2, str3)
    )
    ```

- 补充
    - StringBuilder> StringBuffer> String
    - StringBuilder 为 JDK 5.0 新添加，**非线程安全**，单线程场景推荐优先使用


# 参考
- [深入理解Java中的final关键字](http://www.importnew.com/7553.html)
- [浅析Java中的final关键字](https://www.cnblogs.com/dolphin0520/p/3736238.html)
- [final关键词在多线程环境中的使用](https://blog.csdn.net/xiaoxiaoxuanao/article/details/52573859)
- [为什么String类是不可变的？](http://www.importnew.com/7440.html)
- [如何写一个不可变类？](http://www.importnew.com/7535.html)
- [Java 字符串拼接效率比较](https://blog.csdn.net/Zen99T/article/details/51255418)
- [String,StringBuffer与StringBuilder的区别??](https://blog.csdn.net/rmn190/article/details/1492013)
- [在Java中连接字符串时是使用+号还是使用StringBuilder](http://www.blogjava.net/nokiaguy/archive/2008/05/07/198990.html)
    - 主要针对字符串+/ concat/ append 的几种方式的对比
- [Java中字符串相加和字符串常量相加区别](https://blog.csdn.net/iechenyb/article/details/78202535)
