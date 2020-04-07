---
title: "Hello.License"
date: "2020-03-23"
categories:
 - "整理"
tags:
 - "License"
toc: true
---


## 简述
- 维护自己的权益，避免它人的抄袭盗版和非法商用
- 巩固项目目标，确保跟进项目的持续开源
- 免责声明，避免因其它项目的不当使用陷入纠纷

## 分类

### 快速选择
- [MIT 协议](https://choosealicense.com/licenses/mit/)
    - 免责声明
- [Apache 协议](https://choosealicense.com/licenses/apache-2.0/)
    - 专利作品，免费使用，但需指明作品归属
- [GPL V3](https://choosealicense.com/licenses/gpl-3.0/)
    - 衍生作品的协议需与之一致
- [CC](https://creativecommons.org/licenses/by-nc-nd/4.0/)
    - 知识共享，适合于数据、多媒体、文章等
    - 如知乎和百度等平台上的内容，你和平台都会有相应权限

### 常见分类
- ![Paul Bagwell分析/ 阮一峰汉化](http://doc.yqjdcyy.com/f1176253-ed03-49bd-93ee-6f55e7ba8656.png)

### 协议选择
- 可参见 [Choose an open source license](https://choosealicense.com/)

## 处理
- 代码模块中所用协议的检查
    - 在代码根目录下，使用 `mvn license:add-third-party -Dlicense.useMissingFile`
        - 代码较大的情况下，容易出现 OOM，可临时调整 `MAVEN_OPTS` 配置，或各模块遍历 
- 选择所使用的最严格协议，在最后添加各依赖服务

## 参考
- [当你决定把代码开源之前先选择一个合适的 License](https://zhuanlan.zhihu.com/p/24575976)
    - 示例和警示
- [如何为你的代码选择一个开源协议](https://www.cnblogs.com/wayou/p/how_to_choose_a_license.html)
    - 详尽的各协议授权情况
- [程序员不可不知的版权协议](https://www.gcssloop.com/tips/choose-license)
- [Choose an open source license](https://choosealicense.com/)
    - 协议选择，主要针对开源软件
- [creativecommons](https://creativecommons.org/choose/)
    - 协议选择，提供相应链接格式
- [通过Maven检查三方库依赖的许可](https://ralf0131.github.io/post/third-party-dependency-check/)
- [license:add-third-party](https://www.mojohaus.org/license-maven-plugin/add-third-party-mojo.html)
    - 参数信息详解
- [Third-party](https://www.mojohaus.org/license-maven-plugin/examples/example-thirdparty.html)    
    - 示例参考