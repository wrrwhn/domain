---
title: "Hello.Maven.Repository"
date: "2019-05-28"
categories:
 - "整理"
tags:
 - "Maven"
toc: true
---


# Repository
## 组成
- Local Repository
    - 本地文件夹
    - 默认为 `{m2}\repository`
- Remote Repository
    - 中央仓库
        - http://repo1.maven.org/maven2/ 
    - 私服 
        - http://localhost/maven2/ 
    - 公用仓库
        - https://maven.aliyun.com/repository/central

# 对比
## Mirror
- 作用
    - `mirror` 相关于 `repository` 请求的拦截器
- 目的
    - 针对访问和下载**速度**的考量
- 图示
    - ![000002_d74u_820500.png](http://doc.yqjdcyy.com/bd4fa722-adc9-4cf3-8901-412393ab6f6c.png)
- 注意
    - 镜像会**完全屏蔽**被镜像仓库，按需开关
        - 如若镜像无法访问，而被镜像仓库可访问，仍会因仅无法读取镜像库而报错
    - 镜像库列表展现，但会按 id/ mirrorOf 的匹配处理
        - 镜像有配置针对的环境，因此可理解为特定环境的备份/容灾
    - 镜像可使用 `<mirrorOf>` 来匹配多个类型

        | 写法        | 作用                                                           |
        |-------------|--------------------------------------------------------------|
        | *           | 拦截所有远程仓库                                               |
        | extenrnal:* | 拦截所有非本机的远程仓库<br>跳过 `localhost` 和 `file://` 协议 |
        | repo1,repo2 | 同时拦截使用逗号分隔的多个远程仓库                             |
        | *,!repo1    | 拦截除 repo1 之外的远程仓库                                    |
    
    - Maven 会按镜像 ID 对其排序
        - 理解 `mirrorOf` 均匹配，则会根据 ID 升序查找，仅在上一个无法连接时，继续遍历

# 应用场景
## 根据场景指定不同仓库

- `pom.xml`
```xml
<profiles>
    <profile>
        <id>dev</id>
        <!-- 是否默认启用该配置 -->
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <!-- 激活环境变量 -->
        <properties>
            <env>dev</env>
        </properties>
        <!-- 配置仓库信息 -->
        <distributionManagement>
            <repository>
                <id>nexus-releases</id>
                <name>releases</name>
                <url>http://10.104.114.236:8081/repository/maven-releases/</url>
            </repository>
            <snapshotRepository>
                <id>nexus-snapshots</id>
                <name>snapshots</name>
                <url>http://10.104.114.236:8081/repository/maven-snapshots/</url>
            </snapshotRepository>
        </distributionManagement>
    </profile>
</profiles>
```

- 注意事项
    - 如若配置无误，但仍无法正常切换，需排除一下 `<mirror>` 配置的干扰

# 补充
## 拉取顺序 
- `Local Repository`
- `Mirror`
- `Remote Repository`

# 疑惑
## Mirror.mirrorOf 的默认值
- 查找各资料无果，不知道是否为 `central`

    ```xml
    <xs:element minOccurs="0" name="mirrorOf" type="xs:string">
        <xs:annotation>
            <xs:documentation source="version">1.0.0</xs:documentation>
            <xs:documentation source="description">
            The server ID of the repository being mirrored, eg "central". This MUST NOT match the mirror id.
            </xs:documentation>
        </xs:annotation>
    </xs:element>
    ```

# 参考
- [Maven：mirror和 repository 区别](https://my.oschina.net/sunchp/blog/100634)
- [记录settings.xml的配置，理解mirror、repository、profile的关系](http://www.voidcn.com/article/p-agiwpfud-yy.html)
- [Settings Reference](https://maven.apache.org/settings.html)
- [Using Mirrors for Repositories](http://maven.apache.org/guides/mini/guide-mirror-settings.html)
- [Maven settings配置中的mirrorOf](https://blog.csdn.net/isea533/article/details/21560089)