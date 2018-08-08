---
title: "FFmpeg.Upgrade"
date: "2018-08-07"
categories:
 - "整理"
tags:
 - "FFMPEG"
toc: true
---

# 系统情况

```sh
cat /proc/version
    Linux version 3.10.0-123.9.3.el7.x86_64 (builder@kbuilder.dev.centos.org) (gcc version 4.8.2 20140120 (Red Hat 4.8.2-16) (GCC) ) #1 SMP Thu Nov 6 15:06:03 UTC 2014

lsb_release -a
    LSB Version:    :core-4.1-amd64:core-4.1-noarch
    Distributor ID: CentOS
    Description:    CentOS Linux release 7.0.1406 (Core) 
    Release:        7.0.1406
    Codename:       Core
```


# Make - *Fail*

## 情况
- 安装指定编解码器时，部分必须组件[FriBidi]无法正常安装，导致 FFmpeg 无法正常配置安装

## 流程
### 下载
- 到[官网](http://www.ffmpeg.org/releases/)下载最新版本
- 解压安装

    ```sh
    tar -jxvf ffmpeg-4.0.2.tar.bz2 -C ffmpeg-4.0.2
    cd ffmpeg-4.0.2 && ./configure --prefix=/usr/local/ffmpeg && make && make install
    ```
- 实际线上测试后发现少安装编码解码器
    - `Unknown encoder 'libx264'`
- 重新安装并配置相关编解码器

    ```sh
    ./configure                     \
        --extra-cflags="-I/usr/local/ffmpeg/include"    \
        --extra-ldflags="-L/usr/local/ffmpeg/lib"       \
        --pkg-config-flags="--static"                   \
        --prefix=/usr/local/ffmpeg  \
        --disable-debug             \
        --enable-gpl                \
        --enable-libvorbis          \
        --enable-libvpx             \
        --enable-libx264            \
        --enable-libx265            \
        --enable-libmp3lame

    **ERROR: vorbis not found using pkg-config**
    ```

- 安装相关的必须组件
    - 详情可参照 [FFmpeg-4.0](http://www.linuxfromscratch.org/blfs/view/svn/multimedia/ffmpeg.html)
    - 指令如下

        ```sh
        libogg
            wget http://downloads.xiph.org/releases/ogg/libogg-1.3.3.tar.gz
            tar xzvf libogg-1.3.3.tar.gz
            cd libogg-1.3.3 &&./configure --prefix="/usr/local/ffmpeg" --disable-static && make 
            make install
            make distclean

        libvorbis
            wget http://downloads.xiph.org/releases/vorbis/libvorbis-1.3.6.tar.gz
            tar xzvf libvorbis-1.3.6.tar.gz
            cd libvorbis-1.3.6 && ./configure --prefix="/usr/local/ffmpeg" --disable-static && make
            make install
            make distclean

        fribidi
            wget https://github.com/fribidi/fribidi/releases/download/v1.0.5/fribidi-1.0.5.tar.bz2
            tar xjf fribidi-1.0.5.tar.bz2
            cd fribidi-1.0.5 && ./configure --prefix="/usr/local/ffmpeg" --disable-static && make && make install
            // cd fribidi-1.0.5 && ./configure --prefix="/usr" --disable-static && make && make install
            // cd fribidi-1.0.5 && mkdir -p build && cd build && meson --prefix="/usr/local/ffmpeg" .. && ninja && ninja stall
                // /usr/local/ffmpeg/lib/pkgconfig

        libass
            wget https://github.com/libass/libass/releases/download/0.14.0/libass-0.14.0.tar.xz
            tar xvJf libass-0.14.0.tar.xz
            cd libass-0.14.0 && ./configure --prefix="/usr/local/ffmpeg" --disable-static && make
            make install

        libass 需要 fribidi 0.19 以上版本，但已安装 1.0.5 却并未被识别到
        ```

## FFmpeg-install - *Success*
- 下载[脚本 - https://raw.githubusercontent.com/q3aql/ffmpeg-install/master/ffmpeg-install](https://raw.githubusercontent.com/q3aql/ffmpeg-install/master/ffmpeg-install)
- 更新脚本内参数「PATH_INSTALL」至你须安装路径
    - `vim ffmpeg-install`
- 调整权限 
    - `chmod 733 ffmpeg-install`
- 安装

    ```sh
    # 查看帮助
    ./ffmpeg-install -h
        ./ffmpeg-install: line 68: axel: command not found
        axel disabled
        ./ffmpeg-install: line 76: aria2c: command not found
        aria2c disabled

        ** ffmpeg-install v.1.2 **
        * How to install:
        ffmpeg-install --install (Latest git version)
        ffmpeg-install --install release (Latest stable version)

        * How to update:
        ffmpeg-install --update (Latest git version)
        ffmpeg-install --update release (Latest stable version)

        * How to uninstall:
        ffmpeg-install --uninstall

        * Show help:
        ffmpeg --help

    # 安装最新稳定版本
    ffmpeg-install --install release        
    ```
- **补充**
    - 使用 `make` 方式安装，执行文件路径为 `/usr/local/ffmpeg/bin/ffmpeg`
    - 而使用 `ffmpeg-install` 被安装，执行文件路径为 `/usr/local/ffmpeg`，且无 include 等相关目录


# 参考
## 资源
- [FFmpeg 官方](http://www.ffmpeg.org/releases/)
- [FFmpeg-4.0](http://www.linuxfromscratch.org/blfs/view/svn/multimedia/ffmpeg.html)
    - [libogg-1.3.3](http://www.linuxfromscratch.org/blfs/view/svn/multimedia/libogg.html)
    - [Ninja](https://ninja-build.org/)
    - [ninja-build/ninja-Release](https://github.com/ninja-build/ninja/releases)
- [xiph.org](https://xiph.org/downloads/)

## 教程
- [FFMpeg Install On CentOS 7](https://linuxadmin.io/install-ffmpeg-on-centos-7/)
    - 一键化部署，须按需更新脚本中的目录位置
- [在CentOS上编译安装FFmpeg](http://www.yaosansi.com/post/ffmpeg-on-centos/)
    - 内有 Yum 安装和自行编译安装两种模式，后者有详尽的指令
- [2017年12月14日ubuntu下ffmpeg安装过程记录](https://blog.csdn.net/weixin_41213606/article/details/78801125)