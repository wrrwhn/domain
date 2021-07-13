---
title: "IDEA 查看 sun.* 源码"
date: "2020-04-06"
categories:
 - "整理"
tags:
 - "IDEA"
 - "java"
toc: true
---



> 默认 IDEA 通过反编译方式，解析 src.zip 下的 sun.* 前缀的源码。但反编译出来的代码没有注释，变量名感人，让人头大  
> 可通过下载源码包，将相关代码更新至 src.zip 进行扩展，以便直接查看

# 源码下载
- 可至 [unofficial-openjdk/openjdk](https://github.com/unofficial-openjdk/openjdk) 下载
> 如果仅替换 sun.* 代码，推荐直接切换至 jdk8u/jdk8u 分支进行下载  

    - 源码下载
        - `git clone https://github.com/unofficial-openjdk/openjdk.git`
    - 切换分支
        - `git checkout jdk8u/jdk8u`

# 扩展 `src.zip`
- 拷贝出 `jdk` 下的 `src.zip` 文件，进行备份、解压
    - 如本机中源文件路径为 `D:\server\java\jdk1.8.0_65\src.zip` 
        - 解压为 `D:\server\java\tmp\src`
        - 备份至 `‪D:\server\java\tmp\src-bak.zip`
- 解压 `src.zip` 文件，拷贝源码至解压目录中
    - `\openjdk\jdk\src\windows\classes\sun\*`
    - `\openjdk\jdk\src\share\classes\sun\*`
    - ![Snipaste_2020-04-06_07-49-35.png](http://doc.yqjdcyy.com/b84f0089-ccb5-4d5b-a583-5e02efdc4ab3.png)
- 在解压目录中，选择所有文件夹重新压缩为 `src.zip`
- 将重新压缩后的 `src.zip` 替换掉 `D:\server\java\jdk1.8.0_65\src.zip` 

# 验证
- 重启 IDEA 后，便可在扩展类下 `rt.jar` 中查看到 `sun` 目录
    - ![Snipaste_2020-04-06_07-53-54.png](http://doc.yqjdcyy.com/f478d774-845c-4fb6-ba77-973329abc9e3.png)
- 打开 `FileChannelImpl` 就可以查看到其注释和正常变量名，幸福！
    - ![Snipaste_2020-04-06_07-54-33.png](http://doc.yqjdcyy.com/82f926cd-d047-402b-ba95-8ed5e37c5eac.png)


# 参考
- [IDEA查看Java的sun包下的源码](https://plentymore.github.io/2019/01/04/IDEA%E6%9F%A5%E7%9C%8BJava%E7%9A%84sun%E5%8C%85%E4%B8%8B%E7%9A%84%E6%BA%90%E7%A0%81/)
    - 相较原文，镜像目录和拷贝源均有所调整