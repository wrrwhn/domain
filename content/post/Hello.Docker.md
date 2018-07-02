---
title: "Hello.Docker"
date: "2018-06-14"
categories:
 - "整理"
tags:
 - "Docker"
toc: true
---


# 现状
- 环境配置困难
    - 操作系统
    - 库与组件的安装

# 简介
- Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口

## 用途
- 提供一次性的环境
- 提供弹性的云服务
- 组建微服务架构

## 版本
- 社区版 / Community Edition / CE
- 企业版 / Enterprise Edition / EE

## 关键字
- 镜像
    - image
    - **root 文件系统**
        - 提供容器运行时所需的程序、库、资源、配置文件和运行时的相关参数（匿名卷、环境变量与用户等）
    - **分层存储**
        - **逐层构建**， 上层构建完成后将不再发生变更
            - 某层删除文件，最终容器运行时虽查看不到该文件，但实际文件仍跟随镜像
            - 利于镜像的利用、定制
- 容器
    - 实质是 **进程**，运行于属于自己的独立命名空间

- 仓库
    - 集中存储、分发镜像的服务
    - [<repository.name>/]<image.name>[:<version>]
        - version
            - 默认为 `lastest`

# 对比
| 服务       | 原理                                 | 启动速度                                    | 资源占用                                      | 补充                            |
|------------|--------------------------------------|---------------------------------------------|-----------------------------------------------|---------------------------------|
| 虚拟机     | 在一种操作系统里面运行另一种操作系统 | 慢<br>完整操作系统，系统级别操作步骤无法跳过 | 占用多<br>独占一部分内存和硬盘空间            |                                 |
| Linux 容器 | 对进程进行隔离                       | 快 <br> 启动进程                            | 占用少<br>仅占用需要的资源，多个容器可共享资源 | Linux Containers <br>缩写为 LXC |


# 入门
## 安装
### Linux

- 消除历史版本
    ```
    yum remove docker \
        docker-client \
        docker-client-latest \
        docker-common \
        docker-latest \
        docker-latest-logrotate \
        docker-logrotate \
        docker-selinux \
        docker-engine-selinux \
        docker-engine
    ```
- 配置 Repository 
    - 安装必要组件
        ```
        yum install -y yum-utils \
            device-mapper-persistent-data \
            lvm2
        ```
    - 配置 Reposiotry 地址
        ```
        yum-config-manager \
            --add-repo \
            https://download.docker.com/linux/centos/docker-ce.repo
        ```
    - 禁用或开启相应版本更新
        ```
        yum-config-manager --[disable|enable] docker-ce-[edge|stable|test]
        ```

- 安装社区版
    - 安装最新版本
        ```
        yum install docker-ce
            Retrieving key from https://download.docker.com/linux/centos/gpg
            Importing GPG key 0x621E9F35:
            Userid     : "Docker Release (CE rpm) <docker@docker.com>"
            Fingerprint: 060a 61c5 1b55 8a7f 742b 77aa c52f eb6b 621e 9f35
            From       : https://download.docker.com/linux/centos/gpg

        ```
    - 校验
        - 确认安装完成后，提示的 `Fingerprint` 是否与官方提供编码（下行）一致
            - `060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35`
    - 查询安装指定版本
        ```
        yum list docker-ce --showduplicates | sort -r
            docker-ce.x86_64         `17.06.2.ce`-0.1.rc1.el7.centos          docker-ce-test  
            docker-ce.x86_64         `17.06.1.ce`-1.el7.centos                docker-ce-test  
        yum install docker-ce-<VERSION STRING>
            yum install docker-ce-`17.06.2.ce`
        ```
- 启动
    - `systemctl [start|enable|disable] docker`
    - `systemctl restart docker.service`

- HelloWorld
    - `docker run hello-world`
        ```
        Unable to find image 'hello-world:latest' locally
        latest: Pulling from library/hello-world
        9bb5a5d4561a: Pull complete 
        Digest: sha256:f5233545e43561214ca4891fd1157e1c3c563316ed8e237750d59bde73361e77
        Status: Downloaded newer image for hello-world:latest

        Hello from Docker!
        This message shows that your installation appears to be working correctly.

        To generate this message, Docker took the following steps:
        1. The Docker client contacted the Docker daemon.
        2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
            (amd64)
        3. The Docker daemon created a new container from that image which runs the
            executable that produces the output you are currently reading.
        4. The Docker daemon streamed that output to the Docker client, which sent it
            to your terminal.

        To try something more ambitious, you can run an Ubuntu container with:
        $ docker run -it ubuntu bash

        Share images, automate workflows, and more with a free Docker ID:
        https://hub.docker.com/

        For more examples and ideas, visit:
        https://docs.docker.com/engine/userguide/
        ```

- 检测运行状况
    - `ps aux | grep dockerd`


- Mirror
    vim /etc/docker/daemon.json
        ```
        {
        "registry-mirrors": [
            "https://registry.docker-cn.com"
        ]
        }
        ```
    systemctl daemon-reload
    service docker restart
    docker info
        ```
        Registry Mirrors:
            https://registry.docker-cn.com/
        ```

## 指令
### Repository
- types
    - `docker-ce-edge`
    - `docker-ce-stable`
    - `docker-ce-test`
- command
    - `yum-config-manager --[enable|disable] [repository.type]`

### [Docker](http://domain.yqjdcyy.com/post/hello.docker.command)

## 示例
- `docker version`
    ```
    Client:
        Version:      18.05.0-ce
        API version:  1.37
        Go version:   go1.9.5
        Git commit:   f150324
        Built:        Wed May  9 22:14:54 2018
        OS/Arch:      linux/amd64
        Experimental: false
        Orchestrator: swarm
    ```
- `docker image ls`
    ```
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    hello-world         latest              e38bc07ac18e        2 months ago        1.85kB
    ```

- `docker container run hello-world`
    ```
    Hello from Docker!
    This message shows that your installation appears to be working correctly.

    To generate this message, Docker took the following steps:
    1. The Docker client contacted the Docker daemon.
    2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
        (amd64)
    3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
    4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.

    To try something more ambitious, you can run an Ubuntu container with:
    $ docker run -it ubuntu bash

    Share images, automate workflows, and more with a free Docker ID:
    https://hub.docker.com/

    For more examples and ideas, visit:
    https://docs.docker.com/engine/userguide/
    ```

- `docker container ls --all`
    ```
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
    5465dca0bd3a        hello-world         "/hello"            3 minutes ago       Exited (0) 2 minutes ago                        objective_williams
    dae54d95f428        hello-world         "/hello"            11 minutes ago      Exited (0) 11 minutes ago                       pedantic_fermi
    3f17224734de        hello-world         "/hello"            16 minutes ago      Exited (0) 16 minutes ago                       adoring_johnson
    ```

## [Dockerfile](http://domain.yqjdcyy.com/post/hello.docker.dockerfile/)

## 异常

- `Error response from daemon: No build stage in current context`
    - 信息
        - `Dockerfile lacks FROM instruction`
    - 更正
        - Dockerfile 中必输包含有 `FROM XXX` 信息

- `OCI runtime create failed: container_linux.go:348: starting container process caused "exec: \"/bin/sh\": stat /bin/sh: no such file or directory": unknown`
    - 信息
        - 当前配置
            - `FROM scratch`
        - 资料
            - `FROM scratch which is an empty filesystem`
                - repository `scratch` 中无任何信息
            - `In addition, the scratch image has no tooling at all in it, it's a completely empty filesystem. A tool like mkdir would not exist, and likewise using the shell syntax of RUN mkdir foo would not work because it tries to run this in /bin/sh -c <cmd>, which also does not exist in scratch.`
        - 更正
            - `FROM centos`
                - 更改信息源自当前操作系统

# 参考
- 官方
    - Web
        - [Docker](https://www.docker.com/what-docker)
    - Docs
        - [Get Docker CE for CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
        - [Install Docker for Windows](https://docs.docker.com/docker-for-windows/install/)
        - [Post-installation steps for Linux](https://docs.docker.com/install/linux/linux-postinstall/)
        - [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
    - Repository
        - [Docker.Hub](https://hub.docker.com/)
        - [OFFICIAL REPOSITORY hello-world](https://hub.docker.com/r/library/hello-world/)
        - [kubernetes](https://kubernetes.io/)
        - [CentOS.quay](https://quay.io/repository/)
        - [Google.CONTAINER REGISTRY](https://cloud.google.com/container-registry/)
        - [阿里云镜像库](https://cr.console.aliyun.com)
        - [DaoCloud 镜像市场](https://hub.daocloud.io/)
        - [网易云镜像服务](https://c.163.com/hub#/m/library/)
        - [时速云镜像仓库](https://hub.tenxcloud.com/)
    - Repository.Mirror
        - [阿里云加速器](https://cr.console.aliyun.com/#/accelerator)
        - [DaoCloud 加速器](https://www.daocloud.io/mirror#accelerator-doc)
    - 指令
        - [Docker run reference](https://docs.docker.com/engine/reference/run/)
    
- 补充
    - [Docker 入门教程](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
    - [Docker 微服务教程](http://www.ruanyifeng.com/blog/2018/02/docker-wordpress-tutorial.html)
    - [Docker](https://yeasy.gitbooks.io/docker_practice/introduction/why.html)
    - [Docker 教程](http://www.runoob.com/docker/docker-tutorial.html)
    - [构建自己的 Docker 映像文件](http://www.atjiang.com/build-own-docker-images/)
    - [談談 docker network-alias](http://blog.maxkit.com.tw/2017/04/docker-network-alias_30.html)

    
- 异常
    - [exec: "/bin/sh": stat /bin/sh: no such file or directory](https://github.com/moby/moby/issues/31044)
    - [Is it possible to use a “blank” docker container without any install on it?](https://stackoverflow.com/questions/24866373/is-it-possible-to-use-a-blank-docker-container-without-any-install-on-it)P