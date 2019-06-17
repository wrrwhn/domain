---
title: "Java.Serialize"
date: "2019-06-17"
categories:
 - "整理"
tags:
 - "Java"
 - "Serialize"
toc: true
---

# 思考
- 什么是序列化
- 如何序列化
- 为什么实现 Serializable 就能被序列化
- 自定义序列化

# 理解 
## 什么是序列化
- 描述
    - 序列化是转换对象关键信息为字节流
    - 反序列化则是在获取到字节流后，还原出对象的过程

- 信息内容
    - 全路径类名
    - 成员变量
    - serialVersionUID

- 示例
    - 你整理某一同学的描述（地区+姓名/五官特征/身份证）的过程便是序列化，而你同学根据你提供的描述信息而还原出该同学的信息便是反序列化

- 需要序列化的场景
    - 对象持久化
        - 对象比 JVM 生命周期长（停止运行前将对象序列化+持久化，重启后再行读取+构建出对象）
    - 网络传输/远程调用
        - 编解码透明，不需要关注传输过程（过程均以字节传递）


## 如何序列化
- 类实现 `java.io.Serializable` 接口
    - 作为后序的标识判断
- 使用 `ObjectInputStream` 和 `ObjectOutputStream` 对对象进行序列化和反序列化

## 为什么实现 `Serializable` 就能被序列化
- 在 `ObjectOutputStream.writeObject0(Object, boolean)` 方法指明，当实现 `Serializable` 即可默认调用自定义序列化或默认序列化

    ```java
    if (obj instanceof String) {
        writeString((String) obj, unshared);
    } else if (cl.isArray()) {
        writeArray(obj, desc, unshared);
    } else if (obj instanceof Enum) {
        writeEnum((Enum<?>) obj, desc, unshared);
    } else if (obj instanceof Serializable) {
        writeOrdinaryObject(obj, desc, unshared);
    } else {
        if (extendedDebugInfo) {
            throw new NotSerializableException(
                cl.getName() + "\n" + debugInfoStack.toString());
        } else {
            throw new NotSerializableException(cl.getName());
        }
    }
    ```

## 自定义序列化
###  定义 `readObject` 和 `writeObject` 方法

- 实现

    ```java
    private void readObject(java.io.ObjectInputStream s){...}
    private void writeObject(java.io.ObjectOutputStream s){...}
    ```

- 调用
    - `ObjectInput]Stream` 和 `ObjectOutput]Stream` 内部通过反射进行该方法的调用

- 事例
    - 参见 `java.util.ArrayLis`

### 实现 `Externalizable`

- 实现

    ```java
    public class User implements Externalizable {
        @Override
        public void writeExternal(ObjectOutput out){...}
        @Override
        public void readExternal(ObjectInput in) {...}
    }
    ```

- 补充
    - 实现时，需要保障有**无参构造函数**
    - 优先级较 `Serializable` **高**
        - 同时实现 `Serializable` 和 `Externalizable`，会优先调用 `writeExternal|readExternal` 方法


# 注意
## 不序列化项
- `static` 修饰的属性
    - 反序列化时，以该类型的初始值填充（如int=0，Object=null）
- `transient` 标明不进行序列化的属性
 
## 序列化优先级
- 虚拟机优先尝试调用类所定义的 `readObject` 和 `writeObject` 方法
- 未实现上述方法时，则默认调用 `ObjectOutputStream.defaultWriteObject` 和 `ObjectInputStream.defaultReadObject `

## 父子序列化情况
- 父类实现序列化接口后，而子类未实现
    - 子类自动实现序列化
- 子类实现序列化接口，而父类未实现
    - 父类将不被序列化
    - 必须有**无参构造函数**，否则会抛 `InvalidClassException` 异常

# 参考
- [深入分析Java的序列化与反序列化](https://www.hollischuang.com/archives/1140)
- [Java序列化理解与总结](https://www.jianshu.com/p/ff770511a097)
- []()
- []()