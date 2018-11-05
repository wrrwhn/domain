---
title: "Java.9.Feature"
date: "2017-11-16"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---


# 深入解读 Java 9 新特性

## 参考
- [深入解读 Java 9 新特性](https://mp.weixin.qq.com/s/ivj2SmTZqr5qVfPbauPZxg)
- [Java9先睹为快：JShell动手实践](http://www.importnew.com/16353.html)
- [JavaOne 2016——观众得以一睹JShell的威力](http://www.infoq.com/cn/news/2016/09/JavaOne-2016-Keynote-JShell)


## Jigsaw
- 核心
    -实现 Java 平台模块化系统
        - JPMS (Java Platform Module System)

- 针对问题
    - 易错的 classpath
        - 应用不同部分依赖同一类库的`不同版本`
        - `ClassLoading` 复杂而`不明确`，导致 ClassNotFoundException、 NoClassDefFoundError 等异常
    - JDK 过于臃肿，无法按不同需求进行定制或优化

- 目标
    - 可靠配置
        - 明确模块边界和模块间的依赖关系
    - 强封装性
        - 通过封装模块内部私有细节，避免不希望发生的依赖关系

- 拆分 JDK 增强提议 (JEP)
    - `JEP 261`
        - Module System，实现`模块化系统`；
    - `JEP 200`
        - The Modular JDK，将`JDK`自身进行`模块化`；
    - `JEP 201`
        - Modular Source Code，按照模块化的形式，`重构源代码,`因为现有代码并不是划分到一个一个的模块里的。
    - `JEP 220`
        - Modular Run-Time Images，重新`设计`JDK和JRE的`结构`，定义新的URI scheme操作模块，类和资源（jrt）。
    - `JEP 260`
        - Encapsulate Most Internal APIs，按照`模块化封装性`的要求，将不希望暴露的内部API封装起来，如果确实证明是广泛需要的，就作为公共API公开出来。
    - `JEP 282`
        - jlink: The Java Linker。新的`link`工具

- 模块图
    - ![java.se.ee-graph.png](http://doc.yqjdcyy.com/ecd7a640-92c5-4a5f-b32d-8674be84e8e7.png)

- 开发角度
    - 工具和 API 新增或修改，用以编译、链接和运行时的模块支持
    - Java/ Javac 等增加对模块的支持，支持 module path
    - Java9 增加可选链接阶段 (Linking Phase)，创建最小依赖关系的 Java 运行时

- 示例
    - `jlink`
        ```
        $ jlink --module-path jmods/ \
                    --add-modules java.sql.rowset,java.activation \
                    --output myimage
        $ myimage/bin/java --list-modules
            java.activation@9
            java.base@9
            java.datatransfer@9
            java.logging@9
            java.naming@9
            java.security.sasl@9
            java.sql@9
            java.sql.rowset@9
            java.xml@9
        $ myimage/bin/java –m company.application
        ```

## 类库新特性
- 工具 API
    - `JEP 102`
        - Process API Updates
        - 新增 `ProcessHandle` 抽象，优化 `Process` 扩展，以提升对进程操作的支持
        ```
        ProcessHandle current = ProcessHandle.current();
        current.info()
               .totalCpuDuration()
               .ifPresent(d -> System.out.println("Total cpu duration :" + d));
        current.children()
               .forEach(p -> System.out.println("Pid:" + p.getPid()));
        ```
    - `JEP 269`
        - Convenience Factory Methods for Collections
        - 针对集合的工厂方法优化
        - `Set<String> alphabet = Set.of("a", "b", "c");`

    - `JEP 166`
        - Java 并发(Concurrency)API 更新
        - 最小集合 API 以支持 `Reactive Stream`
            - Flow API= Publisher + Subscriber+ Processor
        - 异步方式处理数据流

- 新标准或协议支持
    - 安全
        - `JEP 219`
            - 支持Datagram Transport Layer Security (DTLS)。
            - 支持DTLS version 1.0 (RFC 4347) and 1.2 (RFC 6347)，以提供安全的UDP传输。
            - 目前，基于UDP实现的类似SIP或者电子游戏协议，被证明是很有实际价值的。
        - `JEP 229`
            - 默认keystore格式从JKS替换为PKCS12。
        - `JEP 244`
            - TLS Application-Layer Protocol Negotiation(ALPN) Extension, 这是完整支持HTTP/2协议的前提之一。
        - `JEP 249`
            - 支持OCSP Stapling for TLS，减少证书状态验证的网络开销，提高性能。
        - `JEP 273`
            - 实现基于DRBG的SecureRandom，对于Deterministic Random Bit Generator (DRBG) 机制，您可以参考NIST 800-90Ar1相关文档。
        - `JEP 287`
            - SHA-3 Hash Algorithms。
    - 网络
        - `JEP 110`
            - HTTP/2 client API，支持 HTTP 1.1，HTTP/2 和 WebSocket协议，实现了全新的 HTTP client API，用于替换老旧的HttpURLConnection
            - 支持同步和异步操作模式，充分利用了 Reactive style 编程
    - 编码
        - `JEP 227`
            - Unicode 7.0
        - `JEP 267`
            - Unicode 8.0
        - `JEP 226`
            - UTF-8 Property Files

- 性能优化
    - `JEP 254`
        - Compact String，Java 9 中，修改 String 实现，以 byte[] 数组和一个编码标记替换 char[] (16 bits) 数组
        - `Latin` 语系编码字符串会`节省近半空间`。
        - 这个修改对 API 的使用者完全透明。
    - `JEP 232`
        - 提高安全应用性能。
        - 开启 `security manager` 通常会导致10-15%的性能下降，`Java 9`的改进显著降低了开销。
        - 优化包括用标准的并发容器替换自定义的同步逻辑，或者去掉不必要的检查等等。
    - `JEP 24`
        - 利用CPU指令优化GHASH和RSA。
        - 充分利用Intel x64或者SPARC CPU的部分新指令。
        - 部分加密函数的性能提高非常显著
            - 在 benchmark 中，相比于 JDK 8，AES 性能提高了至少 8 倍。
            - 这是相对保守的数据，测试数据大多在几十倍提升。

## 语言和工具的变化
- `Jshell`
    - 简单易用的交互式执行 Java 代码
    - ![99b03cecbd2c9cca2f320ed778ba2f09.png](http://doc.yqjdcyy.com/75dc6d1b-1996-403e-b5f9-e3e5fe736abe.png)
- `Multi-Release JAR Files`
    - 扩展 JAR 文件格式，支持不同版本 class 文件共存
    - `release`指令
        - `Javac --release N`
        - = `Javac -source N -target N -bootclasspath rtN.jar`
- `Javadoc Search`

## JVM 领域新特性
- `将 G1 作为默认垃圾收集器`
    - 目前 server 模式的默认选项是 `ParallelGC`= `吞吐量优先`
    - 延迟比吞吐量更能提高用户体验，`G1` 可以直接设定延迟目标，达到延迟 SLA 要求
    - 最坏场景的延迟表现优于 `CMS` = `设计原理导致碎片化问题`

- `AOT`
    - `Ahead-of-Time Compilation`= `提前编译`
    - 利用新的编译工具 `jaotc`，把 `class` 编译成类似类库的文件
        - 编译
            - `aotc –output libHelloWorld.so HelloWorld.clas`
        - 使用
            - `java -XX:AOTLibrary=./libHelloWorld.so HelloWorld`
    - 实验阶段，目前支持 Linux 64 平台
    - 不能直接编译成可执行文件，还需要结合 `Jlink` 或 `JavaPackager`


- `移除过时的GC组合`
    - 移除在JDK 8中标记过时的 `Incremental CMS`= `iCMS`
    - 输入 `java` 不支持的参数，将于`启动阶段`直接`报错`

- `统一日志`
    - 引入适用于 `JVM` 各个模块的通用日志机制
    - 在此基础上解决过于碎片化的 `JVM` 日志选项。

- `JVM 性能优化`
    - `改进竞争锁`
        - 改进高度竞争的 `Java Object Monitor` 性能
    - `Spin-Wait Hints`
        - 循环等待提示
    - `Thread.onSpinWait()`


## Java 发布模式
- 切换到以时间驱动
    - 六个月周期
        - 针对企业需求
    - 三年周期
        - Long Term Support
