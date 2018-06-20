+++
date = "2018-06-21T00:10:00+08:00"
title = "Linux.User"
draft = false
tags = ["整理","Linux"]
share = true
+++


[TOC]

## 参考
- [创建、删除用户](http://blog.csdn.net/beitiandijun/article/details/41678251)
- [Linux系统中切换用户身份su与sudo的用法与实例](http://zebralinux.blog.51cto.com/8627088/1369301)
- [useradd命令](http://man.linuxde.net/useradd)
- [Linux 用户和用户组管理](http://www.runoob.com/linux/linux-user-manage.html)
- [更改Linux用户的登录shell环境](http://blog.51cto.com/linuxzr/737340)
- []()
- []()
- []()



## 指令
### adduser
- DESCRIBE
    - 为创建的用户指定主目录、系统 shell 版本，并要求输入密码
        - 主动调用 `/etc/adduser.conf`
        - 默认创建 `/home/user.name`目录
- FORMAT
    - `adduser <user.name>`
- OPTIONS
    - `--home`
        - 指定创建主目录的路径，默认是在 `/home` 目录下创建用户名同名的目录，这里可以指定；
        - 如果主目录同名目录存在，则不再创建，仅在登录时进入主目录。
    - `--quiet`
        - 即只打印警告和错误信息，忽略其他信息。
    - `--debug`
        - 定位错误信息。
    - `--conf`
        - 在创建用户时使用指定的 configuration 文件。
    - `--force-badname`
        - 默认在创建用户时会进行 `/etc/adduser.conf` 中的正则表达式检查用户名是否合法
            - 如果想使用弱检查，则使用这个选项
            - 如果不想检查，可以将 `/etc/adduser.conf` 中相关选项屏蔽

### useradd
- DESCRIBE
    - 默认无密码、目录和指定 shell 版本
- FORMAT
    - useradd [option] <args>
- OPTIONS
    - `c <备注>`
        - 加上备注文字。备注文字会保存在 `passwd` 的`备注栏`位中；
    - `d <登入目录>`
        - 指定用户登入时的启始目录；
    - `D`
        - 变更预设值；
    - `e <有效期限>`
        - 指定帐号的有效期限；
    - `f <缓冲天数>`
        - 指定在密码过期后多少天即关闭该帐号；
    - `g <群组>`
        - 指定用户所属的群组；
    - `G <群组>`
        - 指定用户所属的附加群组；
    - `m`
        - 自动建立用户的登入目录；
    - `M`
        - 不要自动建立用户的登入目录；
    - `n`
        - 取消建立以用户名称为名的群组；
    - `r`
        - 建立系统帐号；
    - `s<shell>`
        - 指定用户登入后所使用的shell；
    - `u<uid>`
        - 指定用户id。
        - ID 值尽量 `> 500`，以避免冲突
- EXAMPLE
    - `useradd tmpuser -c 'useradd.c for passwd' -u 8888 -M -e 1`
    - `passwd tmpuser`


### usermod
- 参数同 `useradd`
- EXAMPLE
    - `usermod -s /bin/sh tmpuser`
        - `cat /etc/shells`
            - 查看本机支持的 shell 版本


### userdel
- DESCRIBE
    - 删除用户
    - 将用户账号从 `/etc/passwd` 等系统文件中的删除
- FORMAT
    - userdel [option] <user.name>
- OPTIONS
    - `-r`
        - 连同用户的主目录一并删除
- EXAMPLE
    - `userdel tmpuser`

### passwd
- DESCRIBE
    - 口令管理
    - 密码保存在 `/etc/passwd`
        - `用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell`
- FORMAT
    - passwd [option] <user.name>
- OPTIONS
    - `-l`
        - 锁定口令，即禁用账号
    - `-u`
        - 口令解锁
    - `-d`
        - 使账号无口令
    - `-f`
        - 强迫用户下次登录时修改口令
- EXAMPLE
    - `passwd -l tmpuser`


### su
- DESCRIBE
    - 切换用户 switch user
- FORMAT
    - su [-] <user.name>
- OPTIONS
    - `-`
        - 是否切换至新用户工作环境
- EXAMPLE
    - `su - appuser`
        - 切换用户后，同时切换到新用户的工作环境
    - `su appuser`
        - 切换用户后，不改变原用户工作目录、环境变量目录
            - 拥有原用户权限

### sudo
- DESCRIBE
    - 允许其它用户以管理员权限执行指令
    - 需要 Root 使用 `visudo` 编辑 `/etc/sudoers` 才可以授权其它用户
        - `whereis sudoers`
        - `chmod u+w /etc/sudoers`
        - `vim /etc/sudoers`
        - `chmod u-w /etc/sudoers`
- FORMAT
    - sudo [-bhHpV] [-s] [-u <user>] [command]
- OPTIONS
    - `-b`
        - 在后台执行指令。
    - `-h`
        - 显示帮助。
    - `-H`
        - 将HOME环境变量设为新身份的HOME环境变量。
    - `-k`
        - 结束密码的有效期限，也就是下次再执行sudo时便需要输入密码。
    - `-l`
        - 列出目前用户可执行与无法执行的指令。
    - `-p`
        - 改变询问密码的提示符号。
    - `-s`
        - 执行指定的shell。
    - `-u`
        - <用户> 以指定的用户作为新的身份。若不加上此参数，则预设以root作为新的身份。
    - `-v`
        - 延长密码有效期限5分钟。
    - `-V`
        - 显示版本信息。
    - `-S`
        - 从标准输入流替代终端来获取密码
