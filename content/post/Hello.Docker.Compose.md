---
title: "Hello.Docker.Compose"
date: "2018-06-20"
categories:
 - "整理"
tags:
 - "Docker"
toc: true
---


# 简介
- Compose
    - 定义和运行多个 Docker 容器的应用
- Service
    - 应用容器，包括若干运行相同镜像的容器实例
- Project
    - 一组关联的应用容器组成的完整业务单元
    - 于 `docker-compose.yml` 文件中定义

# 安装
- `pip install -U docker-compose`
    ```
    Successfully installed backports.ssl-match-hostname-3.5.0.1 cached-property-1.4.3 certifi-2018.4.16 docker-3.3.0 docker-compose-1.21.2 docker-pycreds-0.3.0 dockerpty-0.4.1 docopt-0.6.2 enum34-1.1.6 functools32-3.2.3.post2 idna-2.6 ipaddress-1.0.22 jsonschema-2.6.0 requests-2.18.4 six-1.11.0 texttable-0.9.1 urllib3-1.22 websocket-client-0.48.0
    ```

- `docker-compose version`
    ```
    /usr/lib/python2.7/site-packages/requests/__init__.py:80: RequestsDependencyWarning: urllib3 (1.22) or chardet (2.2.1) doesn't match a supported version!
    RequestsDependencyWarning)
    docker-compose version 1.21.2, build a133471
    docker-py version: 3.3.0
    CPython version: 2.7.5
    OpenSSL version: OpenSSL 1.0.1e-fips 11 Feb 2013
    ```

# 使用
## 示例
- `docker-compose up -d`
    ```
    /usr/lib/python2.7/site-packages/requests/__init__.py:80: RequestsDependencyWarning: urllib3 (1.22) or chardet (2.2.1) doesn't match a supported version!
    RequestsDependencyWarning)
    Starting server_redis_redis_1 ... done
    Starting server_redis_server_1 ... done
    ```

- `docker container ls --all`
    ```
    CONTAINER ID        IMAGE                 COMMAND                  CREATED              STATUS              PORTS                    NAMES
    b533d0f273ec        server_redis_server   "/bin/sh -c './serve…"   About a minute ago   Up 21 seconds       0.0.0.0:8031->3000/tcp   server_redis_server_1
    8b4b70a02f29        redis                 "docker-entrypoint.s…"   About a minute ago   Up 22 seconds       0.0.0.0:8030->6379/tcp   server_redis_redis_1
    ```

 - `docker-compose stop`
    ```
    /usr/lib/python2.7/site-packages/requests/__init__.py:80: RequestsDependencyWarning: urllib3 (1.22) or chardet (2.2.1) doesn't match a supported version!
    RequestsDependencyWarning)
    Stopping server_redis_server_1 ... done
    Stopping server_redis_redis_1  ... done
    ```

- `docker-compose rm`
    ```
    /usr/lib/python2.7/site-packages/requests/__init__.py:80: RequestsDependencyWarning: urllib3 (1.22) or chardet (2.2.1) doesn't match a supported version!
    RequestsDependencyWarning)
    Going to remove server_redis_server_1, server_redis_redis_1
    Are you sure? [yN] y
    Removing server_redis_server_1 ... done
    Removing server_redis_redis_1  ... done
    ```

# 指令
## docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
- Options
    - `-f, --file FILE`
        - 指定配置文件，默认为 `docker-compose.yml`
    - `-p, --project-name NAME`
        - 指定项目名称，默认为目录名
    - `--project-directory PATH`
        - 指定工具目录，默认于组件的目录
    - `--verbose`
        - 显示更多输出
    - `--log-level LEVEL`
        - 设置日志等级，可选项为 `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`
    - `--no-ansi`
        - 不打印 `ANSI` 控制字符
    - `-v, --version`
        - 打印版本信息
    - `-H, --host HOST`
        - 实例连接的套接字
    - `--tls`
        - 使用 `TLS`
    - `--tlscacert CA_PATH`
        - 仅通过 `CA` 信任签证
    - `--tlscert CLIENT_CERT_PATH`
        - 指定 `TLS` 证书文件
    - `--tlskey TLS_KEY_PATH`
        - 指定 `TLS` 键值文件
    - `--tlsverify`
        - 使用 `TLS`并以此校验远程请求
    - `--skip-hostname-check`
        - 不拿守护进程实例名与客户端证书中指定的名称进行校验
    - `--compatibility`
        - 如果设置，则表示将转换 V3 文件中的部署值至非群集等价物
- Commands
    - `build`
        - [重新]构建服务
    - `bundle`
        - 用组件生成 `Docker` 包
    - `config`
        - 校验、查看组件
    - `create`
        - 创建服务
    - `down`
      - 停止并删除容器、网络、镜像和
    - `events`
        - 从容器接收实时事件
    - `exec`
       - 于运行的容器中执行指令
    - `help`
      - 获取命名的帮助信息
    - `images`
        - 展示所有镜像
    - `kill`
        - 杀死容器
    - `logs`
        - 显示容器输出
    - `pause`
        - 暂停服务
    - `port`
        - 展示指定端口绑定的开放端口
    - `ps`
        - 展示容器列表
    - `pull`
        - 拉取服务镜像
    - `push`
        - 推送服务镜像
    - `restart`
        - 重启服务
    - `rm`
        - 移除已关闭的容器
    - `run`
        - 执行一次性的命令
    - `scale`
        - 设置服务中的容器数量
    - `start`
        - 启动服务
    - `stop`
        - 停止服务
    - `top`
        - 显示运行中的进程
    - `unpause`
        - 不停止服务
    - `up`
        - 创建并启动服务
    - `version`
        - 展示 `Docker-Compose` 版本信息

# 模板
## 参考
- [Compose 模板文件](https://yeasy.gitbooks.io/docker_practice/compose/compose_file.html#configs)

## 注意事项
- 默认模板文件名为 `docker-compose.yml`
- 每个服务都要经过 `image` 指定镜像或 `build` 等以自动构建镜像
    - `build` 情况需要 `Dockerfile` 文件
    - 其中 `Dockerfile` 中设置的选项，如`CMD`, `EXPOSE`, `VOLUME`, `ENV` 等会自动被获取

# 参考
## Project
- [Hello_Docker/server_redis](https://github.com/yqjdcyy/Hello_Docker/tree/master/server_redis)
## Compose
- 官方
    - [Docker Compose](https://docs.docker.com/compose/)
    - [docker/docker.github.io](https://github.com/docker/docker.github.io)
    - [compose-file](https://github.com/docker/docker.github.io/tree/master/compose/compose-file) 
    - [reference](https://github.com/docker/docker.github.io/tree/master/compose/reference)
    
- 参考
    - [Docker 微服务教程](http://www.ruanyifeng.com/blog/2018/02/docker-wordpress-tutorial.html)
    - [Docker Compose 项目](https://yeasy.gitbooks.io/docker_practice/compose/)
    - [docker/compose](https://github.com/docker/compose)
## Python
- [Installing Python 3 on Linux](http://docs.python-guide.org/en/latest/starting/install3/linux/)
- [Linux安装python3.6](https://www.cnblogs.com/kimyeee/p/7250560.html)