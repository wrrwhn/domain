+++
date = "2018-06-14T17:00:00+08:00"
title = "Hello.Docker.Command"
draft = false
tags = ["整理","Docker"]
share = true
+++

[TOC]

# Docker.*

## Outline
|        指令       |                         描述                        |
|-------------------|-----------------------------------------------------|
| docker attach     | 将本地输入、输出和异常流附加到运行状态中的容器      |
| docker build      | 使用 Dockerfile 构建镜像                            |
| docker checkpoint | 管理检查点                                          |
| docker commit     | 将容器的变更构建为新镜像                            |
| docker config     | 管理 Docker 配置                                    |
| docker container  | 管理 Docker 容器                                    |
| docker cp         | 进行容器和本地文件系统间的文件/文件夹的拷贝         |
| docker create     | 创建新容器                                          |
| docker deploy     | 发布新堆或更新已存在的堆                            |
| docker diff       | 检查容器的文件系统中，文件或文件夹的变更情况        |
| docker events     | 获取服务的实时事件                                  |
| docker exec       | 在运行的容器中执行指令                              |
| docker export     | 将容器的文件系统打包至 tar 文件                     |
| docker history    | 显示指定镜像的历史                                  |
| docker image      | 管理镜像                                            |
| docker images     | 列表展示镜像                                        |
| docker import     | 由 原始码(tarball) 中引入内容以创建新的文件系统镜像 |
| docker info       | 展示系统纬度信息                                    |
| docker inspect    | 展示 Docker 的低等级信息                            |
| docker kill       | 摧毁容器                                            |
| docker load       | 将 `tar`/`STDIN` 中加载镜像                         |
| docker login      | 登录                                                |
| docker logout     | 登出                                                |
| docker logs       | 抓取容器日志                                        |
| docker manifest   | 管理镜像的 manifest 列表                            |
| docker network    | 管理网络                                            |
| docker node       | 管理集群节点                                        |
| docker pause      | 暂停指定容器中的所有进程                            |
| docker plugin     | 管理插件                                            |
| docker port       | 列表展示端口映射或指定容器的映射情况                |
| docker ps         | 展示所有进程                                        |
| docker pull       | 拉取注册镜像                                        |
| docker push       | 推送镜像注册                                        |
| docker rename     | 为容器重命名                                        |
| docker restart    | 重启容器                                            |
| docker rm         | 移除容器                                            |
| docker rmi        | 移除镜像                                            |
| docker run        | 在新容器中执行命令行操作                            |
| docker save       | 将镜像通过 `STDOUT`（默认）打包至 `tar`文件中       |
| docker search     | 于 `Docker Hub`中搜索镜像                           |
| docker secret     | 管理 Docker 密钥                                    |
| docker service    | 管理服务                                            |
| docker stack      | 管理 Docker 堆                                      |
| docker start      | 将关闭的容器重新开启                                |
| docker stats      | 通过容器的实时流进行统计                            |
| docker stop       | 关闭运行中的容器                                    |
| docker swarm      | 管理集群                                            |
| docker system     | 管理 Docker                                         |
| docker tag        | 创建标签以指向源镜像                                |
| docker top        | 展示容器内部运行状态的进程                          |
| docker trust      | 管理镜像的信用记录                                  |
| docker unpause    | 取消容器中进程的暂停状态                            |
| docker update     | 更新容器的配置信息                                  |
| docker version    | 显示 Docker 的版本信息                              |
| docker volume     | 管理 volumes                                        |
| docker wait       | 阻塞至镜像停止，并输出其退出状态                    |

## Docker.Run
### `docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]`
- OPTIONS
    - 运行设置
        - `-d`
            - 以分离模式，在后台运行
            - 默认为 `-f`，在前台运行，并可将其附加至控制台
        - `--rm`
            - 指定容器关闭时，自动清除容器，并移除文件系统；一般用于测试的前台运行程序
            - 默认持久化容器，保存所有的用户数据
        - `--name`
            - 用于定位容器，可作用于容器名、UUID 短标识和 UUID 长标识
        - `--pid`
            - 为容器设置进程命名模式，将容器并入其它容器进程（PID）的命名空间
            - `host`
                - 在容器内使用 `host`的 PID 命名空间
        - `--uts`
            - 为容器设置 UTS 命名空间模式
            - `host`
                - 在容器内使用 `host`的 UTS 命名空间
        - `--ipc`
            - 为容器设置 IPC 模式
            - 模式类型
                |         Value          |                    Description                    |
                |------------------------|---------------------------------------------------|
                | ``                     | 使用实例的默认配置                                |
                | `none`                 | 个人私有的 IPC 命名空间, 在 `/dev/shm` 未安装情况 |
                | `private`              | 个人私有的 IPC 命名空间                           |
                | `shareable`            | 个人私有的 IPC 命名空间, 可与其它容器一起使用     |
                | `container: <name/ID>` | 加入其它容器的 IPC 命名空间                       |
                | `host`                 | 使用当前系统的 IPC 命名空间                       |
        - `--restart`
            - 指定容器的重启策略
            - 策略模式
                |           策略           |                           结果                           |
                |--------------------------|----------------------------------------------------------|
                | no                       | 退出时不自动重启；默认配置                               |
                | on-failure[:max-retries] | 仅当退出时异常，并设置相应的重启次数                     |
                | always                   | 无关退出状态，无限重启                                   |
                | unless-stopped           | 无关退出状态，重复重启；仅在容器收到停止指令于实例停止前 |      
        - `--log-driver`
            - 配置日志驱动
            - 驱动类型
                |    驱动   |                                   描述                                   |
                |-----------|--------------------------------------------------------------------------|
                | none      | 禁用容器日志                                                             |
                | json-file | JSON 格式文件形式；默认日志驱动                                          |
                | syslog    | 日志格式输出至 syslog（网络传输日志消息的标准格式）                      |
                | journald  | 日志输出至 journald（Linux的全新系统日志格式）                           |
                | gelf      | 日志输出至 Graylog 或Logstash 的 GELF（Graylog Extended Log Format）节点 |
                | fluentd   | 日志以追加形式输出至 Fluentd 软件                                        |
                | awslogs   | 日志输出至 Amazon CloudWatch Logs                                        |
    - 网络设置
        - `--dns=[]`
        - `--network-alias=[]`
            - 设置网络作用哉别名
            - 便于通过别名找到该容器
        - `--mac-address`
            - 不校验 `MAC` 是否唯一
        - `--ip`
        - `--ip6`
        - `-p <ip:hostPort:containerPort | ip::containerPort | hostPort:containerPort | containerPort>`
            - 将容器暴露的端口转到系统的指定端口
    - 配置设置
        - `--memory=<number>[b|k|m|g]`
            - 分配内存值；默认最小值为 4M
        - `--memory-swap=<number>[b|k|m|g]`
            - 分配 **内存+ 交换区**大小；默认交换区大小同内存
        - `--memory-reservation=<number>[b|k|m|g]`
            - 交换内存的 **非强制设置**，仅确保不会长时间占用值超过该值
        - `--kernel-memory=<number>[b|k|m|g]`
            - 分配内核内存大小
        - `-c, --cpu-shares=[0-1]`
            - 相对 CPU 比例
        - `--cpus=<number>`
            - 分配可用 CPU 个数
            - number为分数，其中值为 `0.000` 表示不限制
        - `--cpu-period=0`
            - 设置 CPU CFS（Completely Fair Scheduler 完全公平调度） **周期限制**
        - `--cpu-quota=0`
            - 设置 CPU CFS（Completely Fair Scheduler 完全公平调度） **周期内，最大调度比例**
            - 示例
                - `--cpu-quota=200,000`
                    - 表示最多可使用 200% 的 CPU 资源
        - `--cpuset-cpus="indexes"`
            - 允许使用的 CPU 集合
            - 示例
                - `--cpuset-cpus="1-3"`
        - `--cpuset-mems=”indexes“`
            - 允许使用的内存节点集合
            - 针对于 NUMA 框架的 CPU
        - 其它暂略
        - 用户内存限制
            |             设置            |                    效果                   |
            |-----------------------------|-------------------------------------------|
            |                             | memory=inf, memory-swap=inf<br>`默认配置` |
            | memory=L, memory-swap= `-1` | memory=L<inf, memory-swap=`inf`           |
            | memory=L                    | memory=L<inf, memory-swap=`2*L`           |
            | memory=L<, memory-swap=S    | memory=L<inf, memory-swap=S<inf, L<=S     |
- TAG
    - 版本号
- ARG
    - `; echo $?`
        - 打印进程结束时的状态码
        - `docker run busybox /bin/sh -c 'exit 3'; echo $?`
            - `3`









## Image.*
### Outline
|        命令行        |                      描述                     |
|----------------------|-----------------------------------------------|
| docker image build   | 根据 `Dockerfile` 来构建镜像                  |
| docker image history | 显示指定镜像的历史                            |
| docker image import  | 引入 `tarball` 的内容来创建文件系统镜像       |
| docker image inspect | 显示镜像的详细信息                            |
| docker image load    | 由 `tar` 或 `STDIN` 来加载镜像                |
| docker image ls      | 展示镜像列表                                  |
| docker image prune   | 移除未使用的镜像                              |
| docker image pull    | 拉取镜像                                      |
| docker image push    | 推送镜像                                      |
| docker image rm      | 移除镜像                                      |
| docker image save    | 将镜像通过 `STDOUT`（默认）打包至 `tar`文件中 |
| docker image tag     | 创建标签以指向源镜像                          |

### docker image build [OPTIONS] [PATH | URL | -]
- OPTIONS
    |          名称，缩写          |                    默认描述                    |
    |------------------------------|------------------------------------------------|
    | --compress                   | 使用 `gzip` 压缩构建内容                       |
    | --disable-content-trust	true | 跳过镜像校验                                   |
    | --file , -f                  | 指定 Dockerfile 路径；默认为 `PATH/Dockerfile` |
    | --no-cache                   | 构建时不使用缓存                               |
    | --quiet , -q                 | 构建成功时，不打印镜像 ID                      |
    | --rm	true                    | 构建成功后，移除移除中间状态容器               |
    | --tag , -t                   | 以 <name>[:<tag>] 形式命名                     |
    | --target                     | 设置构建的目标层次                             |

## Images.*
### `docker images [OPTIONS] [REPOSITORY[:TAG]]`
- OPTIONS
    - Outline
        |   名称,缩写   |                   描述                   |
        |---------------|------------------------------------------|
        | --all , -a    | 展示所有镜像<br>默认隐藏暂停或停止的镜像 |
        | --digests     | 显示摘要信息                             |
        | --filter , -f | 根据条件进行输出过滤                     |
        | --format      | 使用 Go 模板进行图像形式打印             |
        | --no-trunc    | 不进行输出流截取                         |
        | --quiet , -q  | 仅显示数值型 ID                          |

    - `--format`
        |    占位符     |        描述        |
        |---------------|--------------------|
        | .ID           | 镜像 ID            |
        | .Repository   | 镜像仓库           |
        | .Tag          | 镜像标签           |
        | .Digest       | 镜像摘要           |
        | .CreatedSince | 镜像的创建历史时间 |
        | .CreatedAt    | 镜像的创建时间     |
        | .Size         | 镜像所占硬盘大小   |


### 示例
- 获取未打标签的镜像，并进行删除操作
    - `docker images --filter "dangling=true"`
        - 展示所有未打标签的镜像（REPOSITORY:TAG= <none>:<none>）
            ```
            REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
            <none>              <none>              8abc22fbb042        4 weeks ago         0 B
            <none>              <none>              48e5f45168b9        4 weeks ago         2.489 MB
            <none>              <none>              bf747efa0e2f        4 weeks ago         0 B
            <none>              <none>              980fe10e5736        12 weeks ago        101.4 MB
            <none>              <none>              dea752e4e117        12 weeks ago        101.4 MB
            <none>              <none>              511136ea3c5a        8 months ago        0 B
            ```
    - `docker rmi $(docker images -f "dangling=true" -q)`
        ```
        8abc22fbb042
        48e5f45168b9
        bf747efa0e2f
        980fe10e5736
        dea752e4e117
        511136ea3c5a
        ```
- 过滤显示创建于镜像 `image1` 之前的镜像信息
    - `docker images --filter "before=image1"`
        ```
        REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
        image1              latest              eeae25ada2aa        4 minutes ago        188.3 MB
        image2              latest              dea752e4e117        9 minutes ago        188.3 MB    
        ```

- 获取过滤符合 `image*:*est` 规则的镜像信息
    - ` docker images --filter=reference='image*:*est'`
        ```
        REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
        image1              latest              eeae25ada2aa        4 minutes ago        188.3 MB
        image2              latest              dea752e4e117        9 minutes ago        188.3 MB
        image3              latest              511136ea3c5a        25 minutes ago       188.3 MB     
        ```   

- 指定格式展示镜像信息
    - `docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"`
        ```
        IMAGE ID            REPOSITORY                TAG
        77af4d6b9913        <none>                    <none>
        b6fa739cedf5        committ                   latest
        30557a29d5ab        docker                    latest
        ```


    - docker image ls
    - docker image rm <image.name>
    - docker image pull [<image.group>/]<image.name>
        - offical.image.group= library
        - default.group= library
    - docker image build -t <image.name>[:<image.version>] <path>

- container.*
    - docker container ls [--all]
    - docker container [run|exec|rm|start|stop|kill|logs] <container.id>
        - run
            - 新建容器
            - 未找到指定 image 文件时，自动调用 `docker image pull` 从仓库抓取
        - exec
            - 进入一个正在运行的容器
        - rm
            - 终止运行的容器仍会占据硬盘空间
    - docker container cp <container.id>:</path>
        - 将正在运行的容器中的文件，拷贝到本机


# 参考
- 官方
    - 指令
        - [Docker run reference](https://docs.docker.com/engine/reference/run/)       
- 补充         
    - [Docker 运行时资源限制-内存memory、交换机分区Swap、CPU](https://blog.csdn.net/CSDN_duomaomao/article/details/78567859)
    - [Docker: 限制容器可用的 CPU](https://www.cnblogs.com/sparkdev/p/8052522.html)
    - [Docker 资源限制之 CPU](https://blog.opskumu.com/docker-cpu-limit.html)
    - [NUMA简介](http://cenalulu.github.io/linux/numa/)
    - [Docker run参考(10) – 资源使用限制](https://www.centos.bz/2017/01/docker-run-runtime-constraints-on-resources/)
    - [Docker容器对CPU资源隔离的几种方式](http://dockone.io/article/1102)
    - [Linux服务器管理员Journald初级指南](http://os.51cto.com/art/201405/440886.htm)
    - [systemd-journald.service 简介](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/160.html)