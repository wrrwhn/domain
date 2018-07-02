---
title: "Hello.Docker.Dockerfile"
date: "2018-06-20"
categories:
 - "整理"
tags:
 - "Docker"
toc: true
---


# Work
- `Docker` 镜像是由只读的层一层层封装而成的，而其中的层均代码着 `Dockerfile` 指令

# Guidlines
- 保持支持容器可补停止、摧毁，然后重新构建并替换的最小化配置
- 不管实际执行的 `Dockerfile` 在哪，当前目录下的文件和目录都将发送至 `Docker` 守护进程中作为构建上下文中
    - 执行 `docker build` 的当前所在目录，称为构建上下文
    - 默认 `Dockerfile` 文件存放于构建上下文中，但也可通过 `-f` 指令指定


# Command
## Parser directives
- 作用
    - 用于指定 `Dockerfile` 中的转义字符或换行
        - 默认为 `\`
        - 重新定义转义字符为 `` ` ``，主要用于支持 Windows PowerShell脚本，为 `c:\\` 情况
        - 编译时不添加至 `Docker` 层，也不显示作为编译的其中一个步骤
        - 位置应该置于文件顶部，不然当处理完注释、空行或构建指令后, `Docker` 将不再寻找解析器指令
- 指令
    - `# directive=value`
        - 仅可使用一次
        - 非大小写敏感，允许空格等非断行型空白
        - 无效情况
            - `\`(续行符)
            - 重复多次
- 使用
    - ``# escape=[\|`]``

## ENV
- 作用
    - 变量定义
- 定义
    - `ENV variable.name variable.value`
- 使用
    - `${variable}= $variable`
    - `${variable:-default}`
        - = nul!= `$variable` ? `$variable` : `default`
    - `${variable:+default}`
        - = nul!= `$variable` ? `default` : `""`

## FROM
- 作用
    - 指定的构建基础，来源可由任意公共存储库的有效镜像
        - `Dockerfile` 必须以 `FROM` 开始
- 指令
    - `FROM <image>[:<tag>|@<digest>] [AS <name>]`
        - `AS <name>`
            - 可选项，设置当前引用在新构建阶段的名称
                - 可于后续的 `FROM` 和 `COPY`
                - FROM=<name|index>
        - `:<tag>|@<digest>`
            - 可选项，用于指定对应的版本
                - 默认为 `lastest`
                - 当构建器找不到指定版本时，返回异常

## RUN
- 作用
    - 于 `Docker` 新层中中执行脚本
        - `RUN` 的缓存不会在下一步构建操作中自动失效
            - `RUN apt-get dist-upgrade -y` 可于下一步构建中重用
            - `docker build --no-cache` 构建中使用 `--no-cache` 排除 `RUN` 构建中的缓存影响
    - 执行命令并 **创建新镜像层**，经常用于 **安装软件包**
- 指令
    - `RUN [[<shell>] <command>|[[<shell>,]"<command>", "<args...>"]]`
- 使用
    - `RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME'`
    - `RUN ["c:\\windows\\system32\\tasklist.exe"]`
    - `RUN ["echo", "$HOME"]`
        - 相较下例，不会将 `$HOME`变量 **替换为实际值**
    - `RUN ["/bin/bash", "-c", "echo $HOME"]`

## CMD
- 作用
    - 执行版本，目的于提供运行容器的默认启动语句或入口点
        - `Dockerfile` 仅执行一次 `CMD` 语句
            - 存在多条指令的情况， **执行最后一条** 
        - 支持 `ENTRYPOINT ` 情况下，需指定 `JSON` 数组格式
    - 设置容器启动后，默认执行的命令及参数
        - 可被 `docker run` 语句后的命令行参数 **替换**
- 指令
    - `CMD [[<shell>] <command>|[[<shell>,]"<command>", "<args...>"]]`
- 使用
    - `CMD ["executable","param1","param2"]`
        - 执行模式，推荐
            - 执行不调用命令解析器，导致正常脚本执行无法正常运行
                - `CMD [ "echo", "$HOME" ]` 中 `$HOME` 将不会被替换
                - 可使用 `脚本模式` 或 `CMD [ "sh", "-c", "echo $HOME" ]` 方式实现
    - `CMD ["param1","param2"]`
        - `ENTRYPOINT` 使用情况
    - `CMD command param1 param2`
        - 脚本模式
        - linux 默认使用 `/bin/sh -c`，windows 默认使用 `cmd /S /C`


## LABEL
- 作用
    - 为镜像添加标签信息
- 指令
    - `LABEL ( <key>=<value>)+`
- 使用
    - 添加标签
        ``` Docker
        LABEL version="1.0"
        LABEL description="This text illustrates \
                that label-values can span multiple lines."
        ```
    - 查看标签
        - `docker inspect <image.name>`
            ``` Docker
            "Labels": {
                "version": "1.0",
                "description": "This text illustrates that label-values can span multiple lines.",
            },    
            ```

## ~~MAINTAINER~~
- 作用
    - 为镜像添加作者信息
- 指令
    - `MAINTAINER <name>`
- 使用
    - 建议使用 `LABEL` 代替
        - `LABEL maintainer="docker@gmail.com"`

## EXPOSE
- 作用
    - **声明** 容器服务监听的特定网络 **端口**
- 指令
    - `EXPOSE <port> [<port>/<protocol>...]`
        - 协义支持 `TCP` 和 `UCP`
            - 默认为 `TCP`
        - 仅声明占用端口，实际开放请参考 `docker run` 的 `-p` 标签
- 使用
    - `EXPOSE 80/tcp`

## ADD
- 作用
    - 将指定的文件、目录或 **远程文件** 拷贝至镜像文件系统中的相对位置
- 指令
    - `ADD [--chown=<user>:<group>] <src>... <dest>`
        - `[--chown=<user>:<group>]` 仅于 Linux 操作系统中有效
            - 将该文件或文件夹指定给指定的用户名、级名或 UID/GID 组合
                - 默认指定给 UID= GID= 0
                - 通过 `/etc/passwd` 和 `/etc/group` 将用户名、级名转换为 `UID` 和 `GID`
        - 仅能 **拷贝构建上下文中** 的数据，且针对目录只 **拷贝目录内内容** 而不包括文件夹本身
- 使用
    ``` Docker
    ADD hom* /dest/                 # 将以 `hom` 开头的文件都拷贝至 `/dest/`
    ADD --chown=bin files* /dest/   # 将以 `files` 开头的文件均垧上至 `/dest/`，并授权给 `bin:bin` 用户
    ADD test dest/                  # 将 `test` 文件拷贝至 `WORKDIR`/dest/
    ADD test /dest/                 # 将 `test` 文件拷贝至 `/dest/ `
    ```


## COPY
- 作用
    - 将指定的文件、目录拷贝至镜像文件系统中的相对位置
- 指令
    - `COPY [--chown=<user>:<group>] <src>... <dest>`
        - 与 `ADD` 基本相同
            - 较之不支持 **远程文件**
            - 较之支持将可识别的压缩格式（tar/ gzip/ bzip2/ etc）源文件， **自动解压** 至指定目录
- 使用
    - 参照 `ADD`

## ENTRYPOINT
- 作用
    - 配置容器启动时运行的命令
    - 容器启动需 `CMD` 和 `ENTRYPOINT` 中任意一个
- 指令
    - `ENTRYPOINT ["executable", "param1", "param2"]`
        - 执行模式
        - `docker run` 的参数将于 `ENTRYPOINT` 后追加，并覆盖元素
    - `ENTRYPOINT command param1 param2`
        - 脚本模式
        - 阻止 `CMD` 和 `RUN` 中的命令行参数被使用
        - 默认以 `/bin/sh -c` 形式运行，导致无法正常地接收 `docker stop` 的信号
- 使用
    |                   |        NULL       | ENTRYPOINT entry v1 | ENTRYPOINT ["entry", "v1"] |
    |-------------------|-------------------|---------------------|----------------------------|
    | NULL              | ERROR             | /bin/sh -c entry v1 | entry v1                   |
    | CMD ["cmd", "c1"] | cmd c1            | /bin/sh -c entry v1 | entry v1 cmd c1            |
    | CMD ["c1", "c2"]  | c1 c2             | /bin/sh -c entry v1 | entry v1 c1 c2             |
    | CMD cmd c1        | /bin/sh -c cmd c1 | /bin/sh -c entry v1 | entry v1 /bin/sh -c cmd c1 |


## VOLUME
- 作用
    - 指定挂载的主机目录
- 指令
    - `VOLUME ["/data"]`
        - Windows 情况下，需指定 **C盘外** 的 **不存在或空** 的文件夹
        - 目录数据改变的操作需 **于声明之前**
        - 无法指定主机上的对应目录，由 **系统自动生成**
- 使用
    - `VOLUME ["/var/log", "/var/db"]`
        - JSON 形式，以「"」包围，以「,」分隔
    - `VOLUME /var/log /var/db`
        - Plain String 形式，以「空格」分隔
- 对比
    - `docker run -v /data:/app`
    - `docker run --volumes-from <image.id>`
    

## USER
- 作用
    - 用于指定镜像运行或 `RUN/ CMD/ ENTRYPOINT` 的用户[群组]
- 指令
    - `USER <user|UID>[:<group|GID>]`
        - 当用户未配置组的情况下，默认以 `root` 群组名义
        - Windows 环境下，用户需先行创建

## WORKDIR
- 作用
    - 为 `RUN/ CMD/ ENTRYPOINT/ COPY/ ADD` 等指令指定工作目录
- 指令
    - `WORKDIR </path/to/workdir>`
        - 多次使用情况下，均以上一命令结果作为 **相对目录**

## ARG
- 作用
    - 运行时传入参数
        - 构建时以 `--build-arg <varname>=<value>` 形式传入
- 指令
    - `ARG <name>[=<default value>]`
- 使用
    - 定义
        ``` docker
        FROM busybox
        USER ${user:-some_user}
        ARG user
        # ARG.user.defaultValue= some_user, realValue= what_user
        USER $user        
        ```
    - 构建
        ``` docker
        docker build --build-arg user=what_user .
        ```

## SHELL
- 作用
    - 指定默认执行的脚本编译器
- 指令
    - `SHELL ["powershell", "-command"]`
    - `SHELL ["cmd", "/S", "/C"]`


# Dockerignore
| 规则       | 行为                                                                                                                      |
|------------|---------------------------------------------------------------------------------------------------------------------------|
| #comment   | 注释                                                                                                                      |
| */temp*    | 根目录下任何以 `temp` 开头的文件或目录都将被排除<br>例如 `/somedir/temporary.txt` 文件和 `/somedir/temp` 文件夹都将被过滤 |
| */*/temp*  | 根目录下第三层任意以 `temp` 开头的文件或目录都将被排除<br>例如 `/somedir/subdir/temporary.txt`                            |
| temp?      | 根目录下任意以 `temp` 开头，相差以一个字符的文件或目录都将被排除<br>例如 `/tempa and` 和 `/tempb`                          |
| !README.md | 排除例外情况                                                                                                              |


# Example
## Basic
``` docker
# 以镜像 `ubuntu:15.04` 为基础进行封装
FROM ubuntu:15.04
# 将当前目录文件拷贝至 `Docker` 客户端文件夹中
COPY . /app
# 执行命令，编译文件
RUN make /app
# 指定在容器内运行，启动服务
CMD python /app/app.py
```

## Basic.Windows
``` docker
# 将转义字符设置为 `，便于在路径描述中正常使用 \ 
# escape=`

FROM microsoft/nanoserver
COPY testfile.txt c:\
RUN dir c:\
```

## Variable
``` Docker
FROM busybox
ENV foo /bar
ENV abc=hello           # abc= hello
ENV abc=bye def=$abc    # abc= bye; def= hello
ENV ghi=$abc            # ghi= hello

WORKDIR ${foo}          # WORKDIR /bar
ADD . $foo              # ADD . /bar
COPY \$foo /quux        # COPY $foo /quux
```

## Build.Multiple
``` Docker
ARG  CODE_VERSION=latest        # 唯一可以列于 FROM 之前的结构
FROM base:${CODE_VERSION}
CMD  /code/run-app

FROM extras:${CODE_VERSION}
CMD  /code/run-extras    
```

## Basic.Run
``` Docker
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd          # ./a/b/c/pwd
```


# Reference
## 官方
- [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
- [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [.dockerignore file](https://docs.docker.com/engine/reference/builder/#dockerignore-file)

## 整理
- [Docker镜像构建文件Dockerfile及相关命令介绍](https://itbilu.com/linux/docker/VyhM5wPuz.html)
- [Docker入门教程（三）Dockerfile](http://dockone.io/article/103)
- [Dockerfile: ADD vs COPY](https://www.toutiao.com/i6313838452630618626/)
- [每天5分钟玩转 OpenStack](https://www.ibm.com/developerworks/community/blogs/132cfa78-44b0-4376-85d0-d3096cd30d3f/entry/RUN_vs_CMD_vs_ENTRYPOINT_%E6%AF%8F%E5%A4%A95%E5%88%86%E9%92%9F%E7%8E%A9%E8%BD%AC_Docker_%E5%AE%B9%E5%99%A8%E6%8A%80%E6%9C%AF_17?lang=en)
- [docker学习笔记18：Dockerfile 指令 VOLUME 介绍](https://www.cnblogs.com/51kata/p/5266626.html)