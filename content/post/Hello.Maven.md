---
title: "Hello.Maven"
date: "2019-03-09"
categories:
 - "整理"
tags:
 - "Maven"
toc: true
---


# 概述
> Apache Maven is a software project management and comprehension tool. Based on the concept of a project object model (POM), Maven can manage a project's build, reporting and documentation from a central piece of information.

- Maven 作为软件项目的**管理**和**帮助理解**的工具。
- 基于 **`pom.xml`** 文件，Maven 支持进行项目的**建构**/ 上报/ 文档化


> Maven, a Yiddish word meaning accumulator of knowledge, was originally started as an attempt to simplify the build processes in the Jakarta Turbine project. There were several projects each with their own Ant build files that were all slightly different and JARs were checked into CVS. We wanted a standard way to build the projects, a clear definition of what the project consisted of, an easy way to publish project information and a way to share JARs across several projects.

- Maven 意为知识的累加器
- 最初为简化 `Jakarta Turbine` 项目而设计
    - 项目内各子项目，均有各自的 `Ant` 构建文件，且均略有不同
    - 我们想要一种标准的方法来简化项目的**定义**/ **发布**和**共享 Jar** 文件

> Maven’s primary goal is to allow a developer to comprehend the complete state of a development effort in the shortest period of time
>- Making the build process easy 
>> Providing a uniform build system
>> Providing quality project information
>> Providing guidelines for best practices development
>> Allowing transparent migration to new features

| 目标                   | 实现         |
|----------------------|--------------|
| 简化构建流程           | 隐藏潜在原理 |
| 提供统一的构建系统     |通过 pom.plugins 共享所有项目              |
| 提供高效的项目描述     |交叉引用来源<br>依赖列表<br>单测报告<br>源代码处的变更日志<br>项目管理的邮箱列表              |
| 新手实践的最佳指导     |统一文件目录格式<br>独立单测场景，全场景覆盖              |
| 允许透明地迁移至新特性 |              |


# 概念

## 结构

### 项目结构 
- 可由 `mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes` 生成

    ```
    /project
        pom.xml
        /src
            /main
                /java/com/company/app
                    App.java
                /resources
            /test
                /java/com/company/app
                    AppTest.java
                /resources
    ```

### POM 文件结构

- 示例

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>com.group</groupId>
      <artifactId>artifact.id</artifactId>
      <version>4.11</version>
      <scope>test</scope>
      <type>jar</type>
      <exclusions>
        <exclusion>
          <groupId>com.group</groupId>
          <artifactId>artifact.excluded.id</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
  </dependencies>
</project>
```

- 节点含义

| 节点                    | ...                  | 描述                                                                |
|-------------------------|----------------------|---------------------------------------------------------------------|
| project                 |                      |                                                                     |
| modelVersion            |                      | 指定 pom.xml 文件所使用的对象模型的版本                             |
| groupId                 |                      | 项目所属组织或机构的唯一标识                                        |
| artifactId              |                      | 项目的唯一标识名称                                                  |
| packaging               |                      | 指明打包方式<br>JAR(Default)/ WAR/ EAR                              |
| version                 |                      | 指定生成包的版本                                                    |
| name                    |                      | 指定项目名称<br>常用于生成文档中                                    |
| url                     |                      | 指定项目主页<br>常用于生成文档中                                    |
| description             |                      | 提供项目的基础描述<br>常用于生成文档中                              |
| parent                  |                      | 指定父类项目<br>默认继承父类项目的配置                              |
| dependencies.dependency |                      | 指明该项目所依赖的项目信息<br>依此自动下载并构建 classpath          |
|                         | groupId              |                                                                     |
|                         | artifactId           |                                                                     |
|                         | version              |                                                                     |
|                         | type                 | 依赖的类型<br>jar(default)/ war/ ejb-client/ test-jar               |
|                         | scope                | 作用域<br>compile(default)/ runtime/ test/ system/ provided/ import |
|                         | exclusions.exclusion |                                                                     |
| dependencyManagement    |                      | 声明依赖版本，不自动引入<br>子项目可仅标注 group/artifactId 引入     |
| properties              |                      |                                                                     |
| repositories            |                      |                                                                     |
| pluginRepositories      |                      |                                                                     |
| build                   |                      |                                                                     |
| profiles                |                      |                                                                     |
| modules                 |                      |                                                                     |
| prerequisites           |                      |                                                                     |




# 指令
- `mvn --version`

    ```
    Apache Maven 3.5.4 (1edded0938998edf8bf061f1ceb3cfdeccf443fe; 2018-06-18T02:33:14+08:00)
    Maven home: C:\server\maven\apache-maven-3.5.4\bin\..
    Java version: 1.8.0_191, vendor: Oracle Corporation, runtime: C:\server\java\jre1.8.0_191
    Default locale: zh_CN, platform encoding: GBK
    OS name: "windows 10", version: "10.0", arch: "x86", family: "windows"
    ```

- `mvn archetype:generate`

- `mvn comppile`


# 示例

## 模板
- 通用模板
    - `Hello_Java`
        - `mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DparentGroupId=com.yao.java -DparentArtifactId=maven -DgroupId=com.yao.java -DartifactId=maven-archetype -DinteractiveMode=false`

- 自定义模板

    - 模板创建
        - `Hello_Java`
            - `move maven-archetype maven`

        - `Hello_Java\maven\maven-archetype`
            - `mvn archetype:create-from-project`

                ```
                [INFO] Archetype project created in Hello_Java\maven\maven-archetype\target\generated-sources\archetype
                ```
        - `Hello_Java\maven\maven-archetype\target\generated-sources\archetype`
            - `mvn clean install -DskipTest=true`

                ```
                [INFO] --- maven-install-plugin:3.0.0-M1:install (default-install) @ maven-archetype ---
                [INFO] Installing Hello_Java\maven\maven-archetype\target\generated-sources\archetype\target\maven-archetype-1.0-SNAPSHOT.jar to \.m2\repository\com\yao\java\maven-archetype\1.0-SNAPSHOT\maven-archetype-1.0-SNAPSHOT.jar
                [INFO] Installing Hello_Java\maven\maven-archetype\target\generated-sources\archetype\pom.xml to \.m2\repository\com\yao\java\maven-archetype\1.0-SNAPSHOT\maven-archetype-1.0-SNAPSHOT.pom
                [INFO] --- maven-archetype-plugin:3.0.1:update-local-catalog (default-update-local-catalog) @ maven-archetype ---
                ```
    - 套用模板
        - `Hello_Java\maven`
            - `mvn archetype:generate -DarchetypeCatalog=local`
                - 手动选择模板
                - 录入 group/ artifactId/ version 等相关信息即可

        - `Hello_Java\maven`
            - `mvn archetype:generate -DarchetypeCatalog=local -DarchetypeGroupId=com.yao.java -DarchetypeArtifactId=maven-archetype -DgroupId=com.yao.java -DartifactId=maven-archetype-local-command -DinteractiveMode=false`



# 补充
- 依赖传递
    - 最短优先
        - `D 1.0`
            - `A -> B -> C -> D 2.0`
            - `A -> E -> D 1.0`

- 依赖作用域名

| 类型     | 描述                                                            |
|----------|---------------------------------------------------------------|
| compile  | 适用于项目所有的类路径<br>依赖可传递至子项目                    |
| provided | 由 JDK 或容器提供<br>仅适用于编译和测试类路径<br>不可传递       |
| runtime  | 指明编译时不需要<br>包含于运行和测试的类路径，但包含于编译类路径 |
| test     | 指明仅于测试时使用                                              |
| system   | 指明已通过 jar 形式提供                                         |
| import   | 仅提供于 dependencyManagement 中的 pom 类型依赖<br>             |


    - 最小作用域覆盖
        - 同一 jar 于不同依赖中分别标注以 compile 和 runtime，则当前引入的形式为 runtime




# 参考
- []()
- []()
- [Maven Getting Started Guide](https://maven.apache.org/guides/getting-started/)
- [Maven](https://maven.apache.org/ref/3.6.0/maven-model/maven.html)
    - POM.xml 的完整事例
- [Dependency Mechanism](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)
    - 依赖的机制
    - 作用域等各字段的详细解析
- [Maven中dependencyManagement作用说明](https://blog.csdn.net/vtopqx/article/details/79034835)
    - dependencyManagement 声明默认依赖版本
    - 而 dependencies 声明引用时，可仅指定 groupId 和 artifactId 引用
        - 而当引用特定版本时，再指定 version 即可
- []()
- []()
- []()
- []()

- archetype
    - [创建自己的Maven模板](https://blog.csdn.net/teaey/article/details/24184607)
    - [Maven 3: Maven in 5 minutes “mvn archetype:generate…” command NOT WORKING](https://stackoverflow.com/questions/20165674/maven-3-maven-in-5-minutes-mvn-archetypegenerate-command-not-working/26351113)
        - powershell 情况下，参数需用双引号包起来














































## 部署安装
### 下载地址
- http://maven.apache.org/download.cgi

### 安装流程
- 至下载地址下载最新版本Maven压缩包
- 解压至本地文件路径
- 补充环境变量
    - [M2_HOME: C:\Program Files\Apache Software Foundation\apache-maven-3.2.2]
    - [M2:  %M2_HOME%\bin]
    - [MAVEN_OPTS: -Xms256m -Xmx512m]
    - 并于最后于path变量里补充%M2%;
- 控制台输出 mvn -version，若有相关输出则表示安装成功


## [生命周期 ](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)
### 内置构建：
- default：处理项目部署
- clean：清理项目
- site：创建项目的网站文件
### 各生命周期执行内容：
#### Clean Lifecycle
- pre-clean    executes processes needed prior to the actual project cleaning
- clean    remove all files generated by the previous build
- post-clean    executes processes needed to finalize the project cleaning
#### Default Lifecycle
- validate    validate the project is correct and all necessary information is available.
- initialize    initialize build state, e.g. set properties or create directories.
- generate-sources    generate any source code for inclusion in compilation.
- process-sources    process the source code, for example to filter any values.
- generate-resources    generate resources for inclusion in the package.
- process-resources    copy and process the resources into the destination directory, ready for packaging.
- compile    compile the source code of the project.
- process-classes    post-process the generated files from compilation, for example to do bytecode enhancement on Java classes.
- generate-test-sources    generate any test source code for inclusion in compilation.
- process-test-sources    process the test source code, for example to filter any values.
- generate-test-resources    create resources for testing.
- process-test-resources    copy and process the resources into the test destination directory.
- test-compile    compile the test source code into the test destination directory
- process-test-classes    post-process the generated files from test compilation, for example to do bytecode enhancement on Java classes. For Maven 2.0.5 and above.
- test    run tests using a suitable unit testing framework. These tests should not require the code be packaged or deployed.
- prepare-package    perform any operations necessary to prepare a package before the actual packaging. This often results in an unpacked, processed version of the package. (Maven 2.1 and above)
- package    take the compiled code and package it in its distributable format, such as a JAR.
- pre-integration-test    perform actions required before integration tests are executed. This may involve things such as setting up the required environment.
- integration-test    process and deploy the package if necessary into an environment where integration tests can be run.
- post-integration-test    perform actions required after integration tests have been executed. This may including cleaning up the environment.
- verify    run any checks to verify the package is valid and meets quality criteria.
- install    install the package into the local repository, for use as a dependency in other projects locally.
- deploy    done in an integration or release environment, copies the final package to the remote repository for sharing with other developers and projects.   
#### Site Lifecycle
- pre-site    executes processes needed prior to the actual project site generation
- site    generates the project's site documentation
- post-site    executes processes needed to finalize the site generation, and to prepare for site deployment
- site-deploy    deploys the generated site documentation to the specified web server

### 调用
#### 如调用deploy(mvn deploy)便会自动调用前面的几个流程步骤
#### 仍可在同一指令中调用多个操作（多模块场景中会自动递归至各子模块），如 mvn clean install
#### 相关指令
- Clean Lifecycle Bindings
    - clean    clean:clean

- Default Lifecycle Bindings - Packaging ejb / ejb3 / jar / par / rar / war
    - process-resources    resources:resources
    - compile    compiler:compile
    - process-test-resources    resources:testResources
    - test-compile    compiler:testCompile
    - test    surefire:test
    - package    ejb:ejb or ejb3:ejb3 or jar:jar or par:par or rar:rar or war:war
    - install    install:install
    - deploy    deploy:deploy
- Default Lifecycle Bindings - Packaging ear
    - generate-resources    ear:generate-application-xml
    - process-resources    resources:resources
    - package    ear:ear
    - install    install:install
    - deploy    deploy:deploy
- Default Lifecycle Bindings - Packaging maven-plugin
    - generate-resources    plugin:descriptor
    - process-resources    resources:resources
    - compile    compiler:compile
    - process-test-resources    resources:testResources
    - test-compile    compiler:testCompile
    - test    surefire:test
    - package    jar:jar and plugin:addPluginArtifactMetadata
    - install    install:install
    - deploy    deploy:deploy
- Default Lifecycle Bindings - Packaging pom
    - package    site:attach-descriptor
    - install    install:install
    - deploy    deploy:deploy
- Site Lifecycle Bindings
    - site    site:site
    - site-deploy    site:deploy

### 初始化
#### POM文件中补充<packaging>节点，默认值为jar，可选值为jar/war/ear/pom
#### jar方式流程
- process-resources    resources:resources
- compile    compiler:compile
- process-test-resources    resources:testResources
- test-compile    compiler:testCompile
- test    surefire:test
- package    jar:jar
- install    install:install
- deploy    deploy:deploy
#### 使用Plugins来实现各环节的操作步骤，其中若同一环节有多个插件支持，则以POM文件顺序下的第一个进行处理
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-checkstyle-plugin</artifactId>
    <version>${plugin.checkstyle.version}</version>
    <configuration>
        <configLocation>${project.basedir}/build/check-style.xml</configLocation>
        <includeTestSourceDirectory>false</includeTestSourceDirectory>
        <consoleOutput>true</consoleOutput>
        <failsOnError>true</failsOnError>
    </configuration>
    <executions>
        <execution>
            <phase>verify</phase>
            <goals>
                <goal>check</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```


## [POM.XML详解](http://blog.csdn.net/zhuxinhua/article/details/5788546)
### 作用
- 描述项目如下内容：包括配置文件；开发者需要遵循的规则，缺陷管理系统，组织和licenses，项目的url，项目的依赖性，以及其他所有的项目相关因素。
### 结构
```
<project>
    <modelVersion>4.0.0</modelVersion>
    <!-- 基础设置 -->
    <groupId>项目或者组织的唯一标志，也将会是配置生成路径</groupId>
    <artifactId>项目名称</artifactId>
    <version>版本</version>
    <packaging>打包机制，支持pom,jar,maven-plugin,ejb,war,ear,rar,par</packaging>
    <name>用户描述项目名称，可选</name>
    <url>开发团队网站，可选</url>
    <dependencies>
        <!-- 依赖 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.0</version>
            <type>默认 jar，常用类型有 jar,ejb-client,test-jar</type>
            <scope>
                compile     缺省值，适用于所有阶段，会随着项目一起发布。
                provided     类似compile，期望JDK、容器或使用者会提供这个依赖。如servlet.jar。
                runtime     只在运行时使用，如JDBC驱动，适用运行和测试阶段。
                test         只在测试时使用，用于编译和运行测试代码。不会随项目发布。
                system         类似provided，需要显式提供包含依赖的jar，Maven不会在Repository中查找它。
            </scope>
            <optional>
                默认为 false，即子项目默认继承。为 true时则子项目必须显示引入。
            </optional>
        </dependency>
        <dependency>
            <groupId>com.alibaba.china.shared</groupId>
            <artifactId>alibaba.apollo.webx</artifactId>
            <version>2.5.0</version>
            <exclusions>
                <exclusion>        <!-- 移除指定依赖 -->
                    <groupId>com.alibaba.external</groupId>
                    <artifactId>org.slf4j.slf4j-api</artifactId>
                </exclusion>
                ....
            </exclusions>
    </dependencies>
    <!-- 工程为 parent 时，packing 参数值需为 pom，则子项目可继承 dependencies,developers,contributors,plugin lists,reports lists,plugin execution with matching ids,plugin configuration -->
    <parent>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>my-parent</artifactId>
        <version>2.0</version>
        <relativePath>../my-parent</relativePath>
    </parent>
    <dependencyManagement>
        用于帮助管理子项目的依赖，子项目的相关引用 version 将由父项目进行设置
    </dependencyManagement>
    <modules> 与顺序无关，maven 会自动根据依赖关系拓扑排序 </modules>

    <!-- 配置常量，并于 pom 其它地方以 ${file.encoding} 形式调用  -->
    <properties>
        <file.encoding>UTF-8</file_encoding>
    </properties>
    <!--构建设置 -->
    <build>...</build>
    <reporting>...</reporting>
    <!-- 更多项目信息 -->
    <description>...</description>
    <inceptionYear>...</inceptionYear>
    <licenses>...</licenses>
    <organization>...</organization>
    <developers>...</developers>
    <contributors>...</contributors>
    <!-- 环境设置-->
    <issueManagement>...</issueManagement>
    <ciManagement>...</ciManagement>
    <mailingLists>...</mailingLists>
    <scm>...</scm>
    <prerequisites>...</prerequisites>
    <repositories>...</repositories>
    <pluginRepositories>...</pluginRepositories>
    <distributionManagement>...</distributionManagement>
    <profiles>...</profiles>
</project>
```


## 示例
### 外部 Jar 引入本地项目System
- 相对引用
```
<dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>aliyun-java-sdk-dysmsapi</artifactId>
    <version>1.0.0</version>
    <scope>system</scope>
    <systemPath>${basedir}/src/main/lib/aliyun-java-sdk-dysmsapi-1.0.0.jar</systemPath>
</dependency>
```
- 打包时包括在内
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <version>2.10</version>
    <executions>
        <execution>
            <id>copy-dependencies</id>
            <phase>compile</phase>
            <goals>
                <goal>copy-dependencies</goal>
            </goals>
            <configuration>
                <outputDirectory>${project.build.directory}/${project.build.finalName}/WEB-INF/lib</outputDirectory>
                <includeScope>system</includeScope>
            </configuration>
        </execution>
    </executions>
</plugin>
```
