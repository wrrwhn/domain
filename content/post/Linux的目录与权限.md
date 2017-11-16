
+++
date = "2017-08-25T22:07:46+08:00"
title = "Linux 的目录与权限"
draft = false
tags = ["整理","Linux"]
share = true
+++

[TOC]

# Linux 的目录与权限

## 参考
- [Linux 的文件权限与目录配置](http://cn.linux.vbird.org/linux_basic/0210filepermission.php)
- [理解inode](http://www.ruanyifeng.com/blog/2011/12/inode.html)
- [Linux下 通过删除inode来删除文件](http://www.361way.com/rm-file-use-inode/4187.html)


## 使用者与群组
- 文件拥有者
- 群组
    - 当前拥有者所在群组其它成员权限
- 其它人
    - 非当前拥有者所在群组成员的权限

## 文件权限
### 文件属性
- 示例
    - `drwxr-xr-x 2 root root 4096 Aug 24 09:17 wechat-forum-dev`
- 解析
    - `(d)(rwx)(r-x)(r-x)`
        - 档案类型、权限
        - (1)
            - 文件类型
                - `d`
                    - 目录
                - `-`
                    - 文件
                - `l`
                    - link file
                - `b`
                    - 装置文件中可供存储的接口设备
                - `c`
                    - 装置文件中的串行端口设备，如键盘、鼠标
        - (2)
            - 文件拥有者权限
            - 三位固定代表是否可以读、写、执行
            - 其中指定位上若无权限，则以`-`表示
        - (3)
            - 同群组用户权限
        - (4)
            - 非本群组用户权限
    - `2 `
        - 连结数
        - 硬连接到此 inode 文件的数量
    - `root`
        - 档案拥有者
    - `root`
        - 档案所属群组
    - `4096 `
        - 档案大小，单位为字节
    - `Aug 24 09:17 `
        - 最后修改时间
    - `wechat-forum-dev`
        - 档案名称


## 补充
### inode
- 概念
    - inode
        - 存储文件元信息
            - 文件字节数
            - 拥有者用户 ID
            - 文件的组 ID
            - 读、写、执行权限
            - 时间戳
                - ctime
                    - inode 上次变动时间
                - mtime
                    - 文件内容上次变动时间
                - atime
                    - 上次打开时间
            - 链接数
                - 多少个文件名指向这个 inode
                - block 位置
    - Block
        - 8* Sector
    - Sector
        - 扇区
        - 文件在硬盘中的最小存储单元
        - 512 B
- 查看
    - `stat file`
        - 查看文件的 inode 信息
    - `ls -il`
        - 查看 inode 号码
- 补充
    - 移动、重命名，更新文件名，不影响 inode
        - `ls -i`
        - `find ./ -inum <inode> -exec rm -i {} \;`
    - 文件名包含特殊字符，无法删除，但删除 inode 节点仍起到删除作用

### 硬链接
- 概念
    - 多个文件名指向同一 inode
    - 更新文件的连结数
- 指令
    - `ln 已存在文件 硬链接文件`

### 软链接
- 概念
    - A/B 文件的 inode 不一致，但读 A 时自动导向到 B
    - 删除 B 后，访问 A 时会报错
    - 不更新链接数
- 指令
    - `ln -s 已存在文件或目录 软链接文件或目录`
