---
title: "Hello.Docker.Network"
date: "2018-07-02"
categories:
 - "整理"
tags:
 - "Docker"
toc: true
---


# Driver
## Bridge
### 描述
- 软件形式的网络桥接，允许同一网桥内的容器互相通信，并隔离未连接网桥的容器
    - 通过驱动在主机自动安装规则实现
    - 启动 `Docker` 时默认创建，并将新启动的容器连接到它
- **默认** 网络驱动类型

### 场景
- 同一 `Docker` 运行实例中运行的容器

### 示例
``` Docker
docker network create \
    --driver bridge \
    alpine-net
```

### 补充
- `user-defined bridge`
    - 区别
        - 用户自定义桥接网络，**优于** 默认桥接网络
        - 提供了容器间以更好的隔离性与互操作性
            - 容器连接到用户自定义桥接网络，对桥接网络 **内** 的所有容器 **开放所有端口**，对网络 **外**的 **屏蔽所有端口**
        - 自动通过容器的名称或别名，实现类似 `DNS` 的链路解析
    - 示例
        ``` Docker
        # 创建用户定义桥接网络
        docker network create my-net

        # 创建容器并指定于用户定义桥接网络
        docker create --name my-nginx \
        --network my-net \
        --publish 8080:80 \
        nginx:latest

        # 将已运行的容器(连接|取消连接)于用户定义桥接网络
        docker network (connect|disconnect) my-net my-nginx
        ```

## Host
### 描述
- 移除容器间的网络隔离，直接使用主机的网络
    - 于 `Docker 17.06` 及更高版本的 **集群配置** 中可用

### 场景
- 略

### 示例
``` Docker
docker run \
    --net=host  \
    -v $PWD:/data corfr/tcpdump  \
    -i any  \
    -w /data/dump.pcap \ 
    "icmp"
```

## Overlay
### 描述
- 在 `Docker` 守护进程之间创建了分布式网络
    - 确保链接到它的容器安全、准确地通信
    - 主要用于删除容器之前执行系统级路由的需求
- 创建集群或加入已有集群时，会创建如下网络
    - `ingress` 用于处理服务控制和数据传输，默认连接
    - `docker_gwbridge`网桥，用于将集群中涉及的 `Docker` 守护进程连接起来
- 默认开放规则
    - TCP 2377
        - 集群管理通信
    - TCP|UDP 7946
        - 节点间通信
    - UDP 4789
        - 覆盖网络流量

### 场景
- 支持服务 **集群** 彼此通信
- 支持服务集群与独立容器之间的通信
- 支持位于 **不同 `Docker` 守护进程** 的独立容器之间的通信

### 示例
``` Docker
docker network create \
    --driver overlay \
    --ingress \
    --subnet=10.11.0.0/16 \         # 子网
    --gateway=10.11.0.2 \           # 网关
    --opt com.docker.network.driver.mtu=1200 \      # MTU 1200
    my-ingress
```

## Macvlan
### 描述
- 为容器指定 MAC 地址，于自己网络中暴露为一物理设备
    - `Docker` 守护进程支持通过 MAC 地址将通信路由到容器
    - 可使用不同的物理网络接口来隔离 `Macvlan` 网络
- 模式
    - 网络桥接
        - 通过主机上的物理设备通信
    - 802.1q
        - 更细粒度地控制路由和过滤
- 注意事项
    - 合理规划
    - 单物理接口可分配多 MAC 地址
### 场景
- 需要 **直连物理网络** 情况
- 监视网络通信的应用程序

### 示例
``` Docker
docker network create \
    -d macvlan \
    --subnet=172.16.86.0/24 \
    --gateway=172.16.86.1  \
    -o parent=eth0 pub_net
```

## None
### 描述
- 禁用所有网络
    - 集群配置中不可用

### 场景
- 适用于与 **自定义网络驱动** 一同使用

### 示例
``` Docker
docker run \
    --rm -dit \
    --network none \
    --name no-net-alpine \
    alpine:latest \
    ash
```



# Reference
## 官方
- 大纲
    - [Overview](https://docs.docker.com/network/)
    - [Docker container networking](https://docs.docker.com/v17.09/engine/userguide/networking/)
    - [docker network](https://docs.docker.com/engine/reference/commandline/network/)

- 明细
    - [bridge networks](https://docs.docker.com/network/bridge/)
    - [use the host network](https://docs.docker.com/network/host/)
    - [overlay networks](https://docs.docker.com/network/overlay/)
    - [Macvlan networks](https://docs.docker.com/network/macvlan/)
    - [disable container networking](https://docs.docker.com/network/none/)

- 指令    
    - [docker network](https://docs.docker.com/engine/reference/commandline/network/)

## 补充
- [docker 容器的网络模式](http://cizixs.com/2016/06/12/docker-network-modes-explained)
- [Docker Reference Architecture: Designing Scalable, Portable Docker Container Networks](http://success.docker.com/article/networking)
- [Docker container networking](https://docs.docker.com/v17.09/engine/userguide/networking/)