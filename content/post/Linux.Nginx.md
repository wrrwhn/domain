+++
date = "2017-10-04T22:07:46+08:00"
title = "Linux.Nginx "
draft = false
tags = ["整理","Linux"]
share = true
+++

# Linux.Nginx

## 参考
- [反向代理](https://github.com/moonbingbing/openresty-best-practices/blob/master/ngx/reverse_proxy.md)
- [Nginx 配置简述](http://www.barretlee.com/blog/2016/11/19/nginx-configuration-start/)
- [nginx服务器安装及配置文件详解](https://segmentfault.com/a/1190000002797601)
- [nginx服务器详细安装过程](https://segmentfault.com/a/1190000007116797)
- [Centos下 Nginx安装与配置](http://www.jianshu.com/p/d5114a2a2052)
- [nginx 官网](http://nginx.org/)
- [nginx配置location总结及rewrite规则写法](http://seanlook.com/2015/05/17/nginx-location-rewrite/)
- [nginx配置url重写](https://xuexb.com/post/nginx-url-rewrite.html)

## 概念
### 反向代理
- 概念
    - 用代理服务器来接受internet上的连接请求，然后将请求「转发」给内部网络上的服务器，并将从服务器上得到的结果返回给 internet 上请求连接的客户端
    - ![proxy.png](http://otzm88f21.bkt.clouddn.com/0b924f9c-91dd-49cb-af53-0338dea58fd8.png)
- 用途
    - 将防火墙后面的服务器提供给 Internet 用户访问
        - 负载均衡
        - 缓冲

### Nginx


## 安装
- `yum install nginx`
    - 默认安装许多模块，影响以后安装第三方模块

## 指令
- `nginx -s [start|reload|stop]`
    - 通过 `signal` 向 `nginx`发送指令
- `nginx -t`
    - 查看 nginx 配置文件位置

## 基本配置

## 明细配置项
### main
#### Common
- 全局配置，与具体业务无关
- `woker_processes 2`
    - 工作进程的个数
        - 默认设置为 CPU 的核数
            - `grep ^processor /proc/cpuinfo | wc -l`
        - cpu* (`ssl|gzip`? [1|2]: 1)
            - 可以减少I/O操作
- `worker_cpu_affinity 0001 0010 0100 1000`
    - 设置cpu粘性来降低由于高并发情况下多CPU核切换造成的寄存器等现场重建带来的性能损耗
    - 示例为四核情况下配置
- `worker_connections 2048`
    - 每一个worker进程能并发 [处理|发起] 的最大连接数，包括与客户端、后端代理之间的所有连接
    - 记录于 `events` 中
    - 最大连接数计算
        - 反向代理
            - `worker_processes * worker_connections/4`
        - http 服务
            - `worker_processes * worker_connections/2`
        - 不能超过 `worker_rlimit_nofile`
- `worker_rlimit_nofile 10240`
    - 无默认值
    - 上限为操作系统最大的限制 `65535`
- `use epoll`
    - 记录于 `events` 中
    - `Linux` 下使用 `epoll`
    - `OpenBSD|FreeBSD` 系统中使用类 `epoll` 的事件模型 `kqueue`
    - 不支持这些高效模型情况下，才使用 `select`

##### Http
- Http 服务相关配置
- `sendfile on`
    - 开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，减少用户空间到内核空间的上下文切换
    - 进行「下载」等应用磁盘IO重负载应用，可设置为「off」
- `keepalive_timeout 65`
    - 长连接超时时间，单位是秒
    - 过长容易占用资源，过小则影响大文件上传等服务请求
- `send_timeout`
    - 指定响应客户端的超时时间
- `client_max_body_size 10m`
    - 允许客户端请求的最大单文件字节数
- `client_body_buffer_size 128k`
    - 缓冲区代理缓冲用户端请求的最大字节数

#### Http-Proxy
- 反向代理与缓存实现
- `proxy_connect_timeout 60`
    - nginx跟后端服务器连接超时时间
- `proxy_read_timeout 60`
    - 连接成功后，与后端服务器两个成功的响应操作之间超时时间
- `proxy_buffer_size 4k`
    - 从后端realserver读取并保存用户头信息的缓冲区大小
    - 默认与`proxy_buffers`大小相同
- `proxy_buffers 4 32k`
    - 对单个连接缓存来自后端的响应，网页平均在32k以下
- `proxy_busy_buffers_size 64k`
    - 高负荷下缓冲大小
    - `proxy_buffers*2`
- `proxy_max_temp_file_size`
    - 响应内容超出 `proxy_buffers` 时，将部分内容更新至硬盘临时文件中
    - 设置最大临时文件大小
    - 默认1024M，设置为0时表示禁用
- `proxy_temp_file_write_size 64k`
    - 限制每次写临时文件的大小
- `proxy_temp_path`
    - 编译时指定，设置缓存临时文件路径

#### Http-GZip
- `gzip on`
    - 开启gzip压缩输出，减少网络传输
- `gzip_min_length 1k`
    - 设置允许压缩的页面最小字节数
    - 页面字节数从header头得content-length中获取
    - 默认20，建议1K+，避免越压越大
- `gzip_buffers 4 16k`
    - 设置系统获取「几个单位」的缓存用于存储gzip的压缩结果数据流
    - `4 16k`代表以16k为单位，安装原始数据大小以16k为单位的4倍申请内存
- `gzip_http_version 1.0`
    - 识别 http 协议的版本
    - 支持早期的浏览器
- `gzip_comp_level 6`
    - gzip压缩比
    - 1压缩比最小处理速度最快
    - 9压缩比最大但处理速度最慢
- `gzip_types`
    - 匹配mime类型进行压缩
    - 无论是否指定,”text/html”类型总是会被压缩的
- `gzip_proxied any`
    -  Nginx作为反向代理的时候启用
    - 决定开启或者关闭后端服务器「返回的结果是否压缩」
    - 前提是后端服务器必须要返回包含”Via”的 header头
- `gzip_vary on`
    - 在响应头加个 Vary: Accept-Encoding
    - 让前端的缓存服务器缓存经过gzip压缩的页面

### Server
- 指定虚拟主机域名、IP 和端口
- `listen`
    - 默认80，小于1024的要以root启动
    - `listen *:80` `listen 127.0.0.1:80` 等形式
- `server_name`
    - 如 `localhost` `www.example.com`或正则匹配
- `rewrite`
    - `rewrite <rules> <pathes> <types>;`
        - rules
            - 字符串 | 正则表达式
        - pathes
            - 指定匹配路径要更新的地址
            - 建议使用正则，使用捕获分组功能
        - types
            - `last `
                - 表示完成rewrite，无特殊含义
                - 浏览器地址栏URL地址不变
            - `break`
                - 本条规则匹配完成后，终止匹配
                - 浏览器地址栏URL地址不变
            - `redirect`
                - 返回`302临时重定向`，浏览器地址会显示跳转后的URL地址
            - `permanent`
                - 返回`301永久重定向`，浏览器地址栏会显示跳转后的URL地址

### Global-Args
- `$args`
    - 变量等于请求行中的参数，同$query_string
- `$content_length`
    - 请求头中的Content-length字段。
- `$content_type`
    - 请求头中的Content-Type字段。
- `$document_root`
    - 当前请求在root指令中指定的值。
- `$host`
    - 请求主机头字段，否则为服务器名称。
- `$http_user_agent`
    - 客户端agent信息
- `$http_cookie`
    - 客户端cookie信息
- `$limit_rate`
    - 这个变量可以限制连接速率。
- `$request_method`
    - 客户端请求的动作，通常为GET或POST。
- `$remote_addr`
    - 客户端的IP地址。
- `$remote_port`
    - 客户端的端口。
- `$remote_user`
    - 已经经过Auth Basic Module验证的用户名。
- `$request_filename`
    - 当前请求的文件路径，由root或alias指令与URI请求生成。
- `$scheme`
    - HTTP方法（如http，https）。
- `$server_protocol`
    - 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
- `$server_addr`
    - 服务器地址，在完成一次系统调用后可以确定这个值。
- `$server_name`
    - 服务器名称。
- `$server_port`
    - 请求到达服务器的端口号。
- `$request_uri`
    - 包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。
- `$uri`
    - 不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
- `$document_uri`
    - 与$uri相同。

### Upstream
- 针对特定 URL 对应的系列配置项
- `root /var/www/html`
    - 定义服务器的默认网站根目录位置
    - 于 `server` 或 `/` 下生效
- `index index.jsp index.html index.htm`
    - 定义路径下默认访问的文件名
    - `root` 搭配使用
- `proxy_pass http:/backend`
    - 请求转向 `backend` 对应的服务器列表
        - 反向代理
        - 对应 `upstream` 负载均衡器
    - `proxy_pass http://ip:port`
- `proxy_redirect off;`
- `proxy_set_header Host $host;`
- `proxy_set_header X-Real-IP $remote_addr;`
- `proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;`

### Location
- 匹配网页位置
- `~`
    - 执行一个正则匹配，区分大小写
- `~*`
    - 执行一个正则匹配，不区分大小写
- `^~`
    - 普通字符匹配，如果该选项匹配则结束
    - 一般用于匹配目录
- `=`
    - 普通字符精确匹配


### Alias & Root
- Alias
```
location /c/ {
    # 末尾必须加 /
    # http://localhost/c= /a/ 目录
    alias /a/;
}
```
- Root
```
location /c/ {
    # http://localshot/c= /a/c 目录
    root /a/;
}
```


## 示例
### 默认配置
```
user  www www;
worker_processes  2;

error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

pid        logs/nginx.pid;


events {
    use epoll;
    worker_connections  2048;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    # tcp_nopush     on;

    keepalive_timeout  65;

  # gzip压缩功能设置
    gzip on;
    gzip_min_length 1k;
    gzip_buffers    4 16k;
    gzip_http_version 1.0;
    gzip_comp_level 6;
    gzip_types text/html text/plain text/css text/javascript application/json application/javascript application/x-javascript application/xml;
    gzip_vary on;

  # http_proxy 设置
    client_max_body_size   10m;
    client_body_buffer_size   128k;
    proxy_connect_timeout   75;
    proxy_send_timeout   75;
    proxy_read_timeout   75;
    proxy_buffer_size   4k;
    proxy_buffers   4 32k;
    proxy_busy_buffers_size   64k;
    proxy_temp_file_write_size  64k;
    proxy_temp_path   /usr/local/nginx/proxy_temp 1 2;

  # 设定负载均衡后台服务器列表
    upstream  backend  {
              #ip_hash;
              server   192.168.10.100:8080 max_fails=2 fail_timeout=30s ;
              server   192.168.10.101:8080 max_fails=2 fail_timeout=30s ;
    }

  # 很重要的虚拟主机配置
    server {
        listen       80;
        server_name  itoatest.example.com;
        root   /apps/oaapp;

        charset utf-8;
        access_log  logs/host.access.log  main;

        #对 / 所有做负载均衡+反向代理
        location / {
            root   /apps/oaapp;
            index  index.jsp index.html index.htm;

            proxy_pass        http://backend;
            proxy_redirect off;
            # 后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
            proxy_set_header  Host  $host;
            proxy_set_header  X-Real-IP  $remote_addr;
            proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
            proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;

        }

        #静态文件，nginx自己处理，不去backend请求tomcat
        location  ~* /download/ {
            root /apps/oa/fs;

        }
        location ~ .*\.(gif|jpg|jpeg|bmp|png|ico|txt|js|css)$   
        {   
            root /apps/oaapp;   
            expires      7d;
        }
        location /nginx_status {
            stub_status on;
            access_log off;
            allow 192.168.10.0/24;
            deny all;
        }

        location ~ ^/(WEB-INF)/ {   
            deny all;   
        }
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

  ## 其它虚拟主机，server 指令开始
}

```

### 匹配任何以 /images/ 开始的请求，并停止匹配 其它location
```
location ^~ /images/ {...}
```

### 负载均衡
```
upstream myserver; {
    # 指明按照用户 IP 进行分配
    # 可选类型为轮询、指定权重轮询、fair、url_hash 等
    ip_hash;   
    server 172.16.1.1:8001;
    server 172.16.1.2:8002;
    server 172.16.1.3;
    server 172.16.1.4;
}
location / {
    proxy_pass http://myserver;
}
```


### 域名跳转
- 针对非 ROOT 项目
```
server {
    listen  80;
    server_name www.weixinqdh.com weixinqdh.com;

    access_log  /data/service/logs/nginx/weixinqdh.access.log  main;
    error_log  /data/service/logs/nginx/weixinqdh.error.log;

     location / {
        rewrite ^ /wechat-forum-dev redirect;
    }

    location /wechat-forum-dev/ {
        expires         -1;
        proxy_set_header Host   $http_host;
        proxy_pass              http://localhost:9015/wechat-forum-dev/;
        proxy_redirect          off;
        proxy_set_header        X-Real-IP        $remote_addr;
        proxy_set_header        X-Forwarded-For  $proxy_add_x_forwarded_for;
    }
}
```

### 停止后重启，找不到nginx.pid
```
/usr/local/nginx/sbin/nginx -c  /usr/local/nginx/conf/nginx.conf
```


### 资源转跳
```
location ~ ^/upload/(.*)\.(jpg|jpeg|png|gif)$ {
        root         /data/service/tomcat/webapps;
        access_log   off;
        expires      7d;
}

location ~ ^/wechat-forum-dev/upload/(.*)\.(jpg|jpeg|png|gif)$ {
        rewrite /wechat-forum-dev/(.*) /$1 last;
}
```
