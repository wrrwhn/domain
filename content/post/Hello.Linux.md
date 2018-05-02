+++
date = "2018-05-02T16:30:00+08:00"
title = "Hello.Linux"
draft = false
tags = ["整理","Linux"]
share = true
+++

[TOC]

# 进度
## Linux 基础文件
- 14/30
## Linux 架站文件
- 0/ 31
## Linux 安全
- 0/ 1
## Solaris
- 0/ 1

# 概述
## 系统特点
- 开源免费、稳定性
- 安全性
- 处理多并发性能好


## 系统管理事项(kernel)
- 系统呼叫接口
- 行程管理：多任务
- 内存管理：控制内存，虚拟内存
- 档案系统管理：数据I/O及NTFS档案格式硬盘
- 装置驱动
    - ![装置驱动](http://otzm88f21.bkt.clouddn.com/aee190c7-5746-42a6-886d-f45819cba6ea.jpg)


## 系统演化
- Unix - 1973
    - IBM AIX
    - SUM SOLARIS
    - HP HP UNIX
    - 伯克利分校
    - MINIX
        - LINUS LINUX - 1994
            - REDHAT
            - S.U.S.E
            - 红旗

## VMWare安装
    
## 学习流程
- `Linux`平台开发，含`vi`,`gcc`,`gdb`,`make`,`jdk`,`tomcat`,`mysql`和`linux`基本操作
- 加厚`c`语言功底（c专家编程）或`java`语言
- 学习 `Unix` 环境高级编程（Unix环境高级编程）
- Linux应用系统开发或嵌入式开发

## **推荐书籍**
- 《鸟哥的Linux私房菜》
- 《Linux编程从入门到精通》
- 《Linux内核完全剖析》


# 常用概念
## 安装
- yum
    - 进行软件包管理安装，一般用于自动从中央仓库下载安装指令
    - `yum -y install make|patch|ntp`
- make
    - 主要针对tar.gz和tar.bz2打包软件，然后使用[./configure;]make;make install进行安装
    - 其中./configure用于配置软件功能，其参数--prefix用于指定软件安装目录
    - 使用方式
        - `./configure [--prefix=/opt/fcitx 指定安装至/opt/fcitx目录]`

## 软件地址
- [doc2unix](http://linux.softpedia.com/progDownload/Dos2Unix-Download-5519.html)
- [java](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

## 系统指令
### 指令格式
- `command [-options] ...params`
    - `command` 为命令或可执行文件的名称，例如变换路径的命令为 `cd` 等等
    - `[]`并不存在于实际的命令中
        - 加入选项配置时，通常选项前会带 `-` 如 `-h` 
        - 使用选项的完整全名，则选项前带有 `--`, 如`--help`
    - `...params` 为依附在选项后面的参数，或者是 command 的参数； 
    - **命令太长**的时候，可以使用 `\` 来跳脱[Enter]符号，使命令连续到下一行

### 常用指令
#### 系统登录
- `root`
    - 系统管理员账号，同 **Winddows** 的**Administrator** 
    - 建议以其它账号登录后使用 `su -` 命令来切换至root
- `startx`
    - 进入图形界面
- `Ctrl+ Alt+ F2`
    - CentOS进入命令行界面执行语句        
- `Ctrl+ Alt+ F7`
    - 进入图型界面语句
- `shutdown -n now/20/20:00`
    - 立刻关机/20分钟后关机/晚上8点关机
- `reboot`
    - **重启**计算机
- `logout`
    - **注销**
- `passwd`
    - 修改用户口令

#### 系统信息
- `who/ whoami/ id`
    - 用户基本信息查看
- `uname/uname -a/uname --all`
    - 显示之前操作系统名称
- `firefox/firefox &`
    - 打开火狐/后台打开火狐浏览器   
- `env`
    - 查看当前用户使用的所有环境变量
- `ls/ls -a`
    - 显示当前文件夹内文件/显示含隐藏文件信息
- `echo $SHELL`
    - 显示当前系统shell版本
- `date`
    - 显示当前系统时间
        - `date +%Y/%m/%d`
            - `Fri Sep 13 21:34:18 CST 2013 -> 2013/09/13`
- `cal`
    - `cal [month] [year]`
        - 查看某年某月的日历功能
- `bc`
    - 计算器，默认精度为整数，可用scale=X来调整精度，使用完成输入quit退出
 
#### 快捷操作
- `[tab][tab]`
    - 一次补全，两次显示所有此前缀指令
- `Ctrl+ C`
    - 中断当前指令
- `Ctrl+ D`
    - 退出当前文本文字接口，离开
- CRT下进行复制粘贴
    - 鼠标选中后执行 **Ctrl+ Insert** 进行 **复制**，执行 **Shift+ Insert** 进行 **粘贴**
 

#### 帮助
- `man`
    - 查询当前指令的说明，包括分类（DATE(1)）、完整名、选项、参数等
    - 匹配按键
        - `/String`
            - 向下搜索string字符串
        - `?String`
            - 向上搜索String字符串
        - `q`
            - 结束此次man page
    - 其中DATE(1)中1所对应的参数如下，输入 man 7 man 可以获取完整定义
        - 1    
            - 使用者在shell环境中可以操作的命令或可运行文件
        - 2    
            - 系统核心可呼叫的函数与工具等
        - 3    
            - 一些常用的函数(function)与函式库(library)，大部分为C的函式库(libc)
        - 4    
            - 装置文件的说明，通常在/dev下的文件
        - 5    
            - 配置文件或者是某些文件的格式
        - 6    
            - 游戏(games)
        - 7    
            - 惯例与协议等，例如Linux文件系统、网络协议、ASCII code等等的说明    
        - 8    
            - 系统管理员可用的管理命令
        - 9    
            - 跟kernel有关的文件


### 指令详解
#### 档案与目录管理
- 目录与路径
    - 路径概念
        - 绝对路径
            - 由根目录 `/` 写起
                - `/usr/share/doc`
        - 相对路径
            - 相对于目前工作目录的路径
                `/usr/share/doc`  
                    - `cd /usr/share/man`
                    - `cd ../man`
    - 目录操作
        - 目录指令
            - `cd`
                - 变换目录
            - `pwd [-P]`
                - 显示目前的联结目录名称
            - `mkdir [-mp] dirName`
                - 建立一个新的目录[-m用于设定档案权限，-p则可递归建立文档]
            - `rmdir[-p]`
                - 删除一个空的目录[-p用于递归删除档案]

        - 特殊目录
            - `.` 
                - 代表此层目录
            - `..` 
                - 代表上一层目录
            - `-` 
                - 代表**前一个工作目录**
            - `~` 
                - 代表**目前使用者身份所在的Home目录**
            - `~account` 
                - 代表 account 这个使用者的家目录
    
    - 环境变量
        - `echo $PATH`
            - 输出环境变量
        - `PATH="$PATH":/root`
            - 将/root目录添加至环境变量中，其中以`:`间隔各环境变量


#### 档案与目录管理

- 档案与目录的检视
    - `ls [-aAdfFhilRS] [--color={none, auto, always}] [--full-time]`
        - 目录名称 --> 预设显示：非隐藏档的档名、 以档名进行排序及文件名代表的颜色显示
        - 参数
            - `-a`
                - 全部的档案，连同隐藏档( 开头为 . 的档案) 一起列出来
                - `388b9369-8237-4f6b-9948-a1c6baae1617_basic.mp4`
            - `-A`
                - 全部的档案，连同隐藏档，但**不包括 `.` 与 `..` 这两个目录**
            - `-d`
                - 仅列出目录本身，而不是列出目录内的档案数据
            - `-f`
                - 直接列出结果，而不进行排序，**速度快**
            - `-F`
                - 根据档案、目录等信息，给予附加数据结构
                    - `*`
                        - 代表可执行档
                        - `iusql*`
                    - `/`
                        - 代表目录
                        - `3c15cab3-4eb2-400d-84a4-2af1191cd6be_640x360/`
                    - `=`
                        - 代表 socket 档案
                        - `rpcbind.sock=`
                    - `|`
                        - 代表 FIFO 档案
                        - `dmeventd-client|`
            - `-h`
                - 将**档案容量**以人类较易读的方式(例如 **GB, KB** 等等)列出来
                - `-rw-rw-r-- 1 appuser appuser 1.2M Jan 26 10:56 ff2ba789-09ba-49c1-8b3f-b27039fd9ef4_640x360.mp4`
            - `-i`
                - 列出 inode 位置，而非列出档案属性
                - `8076778 3c15cab3-4eb2-400d-84a4-2af1191cd6be_640x360.mp4`
                - `8076782 3c15cab3-4eb2-400d-84a4-2af1191cd6be_640x360`
            - `-l`
                - **长数据**形式，包含档案的属性等等数据
                - `-rw-rw-r-- 1 appuser appuser  4042068 Jan 22 14:59 fe5c76c7-b1d5-42e0-9ad4-2567a6d14ccb_640x360.mp4`
            - `-n`
                - 列出 UID 与 GID 而非使用者与群组的名称
                - `-rw-r--r-- 1 1001 1001  2293727 Oct 19  2017 ffb2dfd0-7c0f-48a2-85a2-c854a2d1f988_640x360.mp4`
            - `-r`
                - 将排序结果**反向输出**
                - `ls -l`
                    ```                    
                    drwxrwxr-x    2 appuser appuser  4096 May  1 23:32 zip
                    drwxrwxr-x  460 appuser appuser 20480 Apr 27 11:22 video
                    drwxrwxr-x    2 appuser appuser 12288 May  1 23:37 record
                    drwxrwxr-x  512 appuser appuser 90112 May  2 09:09 img
                    drwxrwxr-x  688 appuser appuser  8192 May  1 23:32 hls
                    drwxrwxr-x 3272 appuser appuser 57344 May  1 23:31 doc
                    drwxrwxr-x    2 appuser appuser  8192 Apr 27 14:56 audio
                    ```
                - `ls -rl`
                    ```
                    drwxrwxr-x    2 appuser appuser  8192 Apr 27 14:56 audio
                    drwxrwxr-x 3272 appuser appuser 57344 May  1 23:31 doc
                    drwxrwxr-x  688 appuser appuser  8192 May  1 23:32 hls
                    drwxrwxr-x  512 appuser appuser 90112 May  2 09:09 img
                    drwxrwxr-x    2 appuser appuser 12288 May  1 23:37 record
                    drwxrwxr-x  460 appuser appuser 20480 Apr 27 11:22 video
                    drwxrwxr-x    2 appuser appuser  4096 May  1 23:32 zip
                    ```
            - `-R`
                - **递归子目录**内容进行展示
                - `ls -R`
                    ```
                    ./shell:
                    curl-cookie-test  watermark-run.sh  watermark.sh

                    ./to:
                    preview-slide-rId2.jpg  preview-slide-rId3.jpg  preview-slide-rId4.jpg  slide-rId4-35.jpg  slide-rId4-55.jpg  slide-rId4-75.jpg
                    ```
            - `-S`
                - 以档案**容量大小排序**
                - `ls -lhS`
                    ```
                    total 588K
                    -rw-rw-r-- 1 appuser appuser 124K Oct 19  2017 preview-slide-rId4.jpg
                    -rw-rw-r-- 1 appuser appuser 118K Dec 25 16:15 slide-rId4-75.jpg
                    -rw-rw-r-- 1 appuser appuser  99K Dec 25 16:15 slide-rId4-55.jpg
                    -rw-rw-r-- 1 appuser appuser  89K Oct 19  2017 preview-slide-rId3.jpg
                    -rw-rw-r-- 1 appuser appuser  80K Oct 19  2017 preview-slide-rId2.jpg
                    -rw-rw-r-- 1 appuser appuser  70K Dec 25 16:15 slide-rId4-35.jpg
                    ```
            - `-t`
                - 依**时间排序**
                - `ls -lt`
                    ```
                    -rw-rw-r-- 1 appuser appuser  70698 Dec 25 16:15 slide-rId4-35.jpg
                    -rw-rw-r-- 1 appuser appuser 101205 Dec 25 16:15 slide-rId4-55.jpg
                    -rw-rw-r-- 1 appuser appuser 120465 Dec 25 16:15 slide-rId4-75.jpg
                    -rw-rw-r-- 1 appuser appuser 125989 Oct 19  2017 preview-slide-rId4.jpg
                    -rw-rw-r-- 1 appuser appuser  90566 Oct 19  2017 preview-slide-rId3.jpg
                    -rw-rw-r-- 1 appuser appuser  81338 Oct 19  2017 preview-slide-rId2.jpg
                    ```
            - `--color=never`
                - 不要依据档案特性给予颜色显示；
            - `--color=always`
                - 显示颜色
            - `--color=auto`
                - 让系统自行依据设定来判断是否给予颜色
            - `--full-time`
                - 以**完整时间**模式 (包含年、月、日、时、分) 输出
                - `ls --full-time`
                    ```
                    drwxrwxr-x 5 appuser appuser 4096 2018-03-16 12:27:05.786003128 +0800 from
                    drwxrwxr-x 2 appuser appuser 4096 2017-10-19 12:00:34.242962752 +0800 shell
                    drwxrwxr-x 2 appuser appuser 4096 2017-12-25 16:15:56.524407396 +0800 to
                    ```
            - `--time={atime,ctime}`
                - 输出 access 时间或 改变权限属性时间 (ctime)而非内容变更时间 (modification time)


- 复制、移动与删除
    - `cp [-adfilprsu] 来源档 * (source) 目的檔(destination)`
        - 复制档案或目录
        - 参数
            - `-a`
                - 相当于 -pdr 的意思；
            - `-d`
                - 若来源文件为连结文件的属性(link file)，则复制连结文件属性而非档案本身；
            - `-f`
                - 为强制 (force) 的意思，若有重复或其它疑问时，不会询问使用者，而强制复制；
            - `-i`
                - 若目的檔(destination)已经存在时，在**覆盖**时会先**询问**是否真的动作！
            - `-l`
                - 进行硬式连结 (hard link) 的连结档建立，而非复制档案本身；
            - `-p`
                - 连同档案的属性一起复制过去，而非使用预设属性；
            - `-r`
                - 递归持续复制，用于目录的复制行为；
            - `-s`
                - 复制成为符号连结文件 (symbolic link)
                - 快捷方式
            - `-u`
                - 若 destination 比 source 旧才更新
    - `rm [-fir] 档案或目录`
        - 移除档案或目录
        - 参数
            - `-f`
                - 就是 force 的意思，**强制移除**
            - `-i`
                - 互动模式，在删除前会询问使用者是否动作
            - `-r`
                - **递归删除**

     - `mv [-fiu] source1 source2 source3 .... destination`
        - 移动档案与目录，或更名
        - 参数
            - `-f`
                - force 强制的意思，**强制移动**而不询问
            - `-i`
                - 若目标档案 (destination) 已经存在时，就会**询问是否覆盖**
            - `-u`
                - 若目标档案已经存在，且 source 比较新，才会更新 (update)

- 取得路径的文件名称与目录名称
    - 获取文件名称
        - `basename /data/test/resource`
            - `resource`
    - 获取目录名称
        - `dirname /data/test/resource`
            - `/data/test`


#### 档案内容查阅

- 直接检视档案内容
    - `cat [-AEnTv] file`
        - 由第一行开始显示档案内容
        - 参数
            - `-A`
                - 相当于 `-vET` 的整合参数，可列出一些特殊字符
            - `-E`
                - 将结尾的断行字符 $ 显示出来
            - `-n`
                - 打印出行号
            - `-T`
                - 将 [tab] 按键以 ^I 显示出来
            - `-v`
                - 列出一些看不出来的特殊字符

    - `tac /etc/issue`
        - 与cat相反，从最后一行开始显示

    - `nl [-bnw] 文件`
        - 带行号进行显示
        - 参数
            - `-b`
                - 指定行号指定的方式，主要有两种
                - `-b a`
                    - 表示不论是否为空行，也同样列出行号
                - `-b t`
                    - 如果有空行，空的那一行不要列出行号
            - `-n`
                - 列出行号表示的方法，主要有三种
                - `-n ln`
                    - 行号在屏幕的**最左方**显示
                - `-n rn`
                    - 行号在自己字段的**最右方**显示，且不加 0 
                - `-n rz`
                    - 行号在自己字段的最右方显示，且加 0 
            - `-w`
                - 行号字段的占用的位数。

- 可翻页检视
    - `more file`
        - 一页一页的显示档案内容
        - 操作
            - `空格键 (space)`
                - 代表**翻页**
            - `Enter`
                - 代表**移行**
            - `/字符串 `
                - 代表在这个显示的内容当中，向下搜寻『字符串』
            - `:f `
                - 立刻显示出**文件名及当前行数**
            - `q `
                - 代表立刻离开 more ，不再显示该档案内容

    - `less file`
        - 在 `more` 的基础上支持向前翻页
        - 操作
            - `[pagedown]`
                - 向下翻动一页
            - `[pageup]`
                - 向上翻动一页
            - `/字符串`
                - 向下搜寻『字符串』的功能
            - `?字符串`
                - 向上搜寻『字符串』的功能
            - `n`
                - 重复前一个搜寻 (与 / 或 ? 有关！)
            - `N`
                - 反向的重复前一个搜寻 (与 / 或 ? 有关！)   

- 资料撷取
    - `head [-n number] file`
        - 显示指定文件前number行（默认前十行）
    - `tail [-cfFnqsv] file`
        - 显示指定文件右方数据，默认显示十行
        - 参数
            - `f`
                - 实时显示最新追加的数据
            - `n line-rows`
                - 显示指定行数的数据

- 非纯文字文件
    - `od [-t TYPE] file`
        - 以二进制读取文档内容

- 修改档案时间和建置新档
    - 档案变动时间
        - mtime
            - 修改档案更新时间
        - ctime
            - 状态（如权限属性）变更时记录时间
        - atime
            - 阅读档案时间
    - `touch [-acdmt] file`
        - 调整档案时间


#### 档案与目录的预设权限与隐藏权限

- 权限配置
    - `chrown [-Rf/abc] user[:group] file*/file1 file2..`
        - 参数
            - `-f`
                - 忽略错误信息
            - `-h`
                - 修复符号链接
            - `-R`
                - 处理指定目录以及其子目录下的所有文件
            - abc各数值分别对应User/Group/Other的权限
                - 读r=4,写w=2,执行x=1
                - 常用权限如下
                    - `-rw------- (600)`
                        - 只有属主有读写权限。 
                    - `-rw-r--r-- (644)`
                        - 只有属主有读写权限；而属组用户和其他用户只有读权限。 
                    - `-rwx------ (700)`
                        - 只有属主有读、写、执行权限。 
                    - `-rwxr-xr-x (755)`
                        - 属主有读、写、执行权限；而属组用户和其他用户只有读、执行权限。 
                    - `-rwx--x--x (711)`
                        - 属主有读、写、执行权限；而属组用户和其他用户只有执行权限。 
                    - `-rw-rw-rw- (666)`
                        - 所有用户都有文件读、写权限。这种做法不可取。 
                    - `-rwxrwxrwx (777)`
                        - 所有用户都有读、写、执行权限。更不可取的做法。 

- 档案预设权限
    - `umask [-S] [abcd]`
        - 设置限制新建文件权限的掩码，以决定新文件被创建时的最初的权限
        - 其中abcd分别对应特殊权限/User/Group/Other移除项属性值，其中档案默认最大权限为666，而目录为777
        - 参数
            - `-S`
                - 以符号方式输出权限掩码
                - `u=rwx,g=rx,o=rx`
        - 示例
            - `umask u=, g=w, o=rwx`
            - `umask 0022`
            - `ll `
                - `drwxr-xr-x. 2 root root 4096 9月  28 12:02 resource`
            - `umask 007`
            - `mkdir test4Umask`
            - `ll`
                - `drwxr-xr-x. 2 root root 4096 9月  28 12:02 resource`
                - `drwxrwx---. 2 root root 4096 9月  28 17:55 test4Umask`
            - `umask 0022`
​

- 设置档案隐藏属性
    - `chattr [-RVf] [-+=aAcCdDeijsStTu] [-v version] files...`
        - 参数
            - `-`
                - 递归处理所有的文件及子目录。　　
            - `-`
                - 详细显示修改内容，并打印输出。
            - `+`
                - 增加某一个特殊参数，其它原本存在参数则不动。
            - `-`
                - 移除某一个特殊参数，其它原本存在参数则不动。
            - `=`
                - 设定一定，且仅有后面接的参数
            - `A`
                - 当设定了 A 这个属性时，这个档案(或目录)的存取时间 atime (access)不可被修改，可避免例如手提式计算机容易有磁盘 I/O 错误的情况发生！
            - `S`
                - 这个功能有点类似 sync 的功能！就是会将数据同步写入磁盘当中！可以有效的避免数据流失！
            - `a`
                - 当设定 a 之后，这个档案将只能增加数据，而不能删除
                - 只有 root 才能设定这个属性。
            - `c`
                - 这个属性设定之后，将会自动的将此档案『压缩』，在读取的时候将会自动解压缩，但是在储存的时候，将会先进行压缩后再储存(看来对于大档案似乎蛮有用的！)
            - `d`
                - 当dump(备份)程序被执行的时候，设定d属性将可使该档案(或目录)不具有dump功能
            - `i`
                - 让一个档案**不能被删除、改名、设定连结也无法写入或新增资料**
            - `j`
                - 当使用 ext3 这个档案系统格式时，设定 j 属性将会使档案在写入时先记录在journal 中
                - 但是当 filesystem 设定参数为 data=journalled 时，由于已经设定了日志了，所以这个属性无效！
            - `s`
                - 让系统在删除这个文件时，使用0填充文件所在的区域
            - `u`
                - 当一个应用程序请求删除这个文件，系统会保留其数据块以便以后能够恢复删除这个文件
        - 示例
            - `chattr +i chattr-test.dat`
                - `-rw-r--r-- 1 appuser appuser       2 May  2 11:12 chattr-test.dat`

        - 注意
            - 这个属性设定上面，比较常见的是 a 与 i 的设定值，而且很多设定值必须要身为 **root** 才能够设定的！


- 显示档案隐藏属性
    - `lsattr [-aR] file/dir`
        - 参数
            - `-a`
                - 将隐藏文件的属性也显示出来
            - `-R`
                - 连同子目录的数据也一并列出来
        - 示例
            - `lsattr -a`
                ```
                ----i--------e-- ./chattr-test
                -------------e-- ./..
                -------------e-- ./slide-rId3-blue.jpg
                ----i--------e-- ./chattr-test.dat
                ```
​
- 档案特殊权限
    - 示例
        - `ll /tmp/ /usr/bin/passwd`
            - `drwxrwxrwt.  11 root root  4096 9月  28 17:00 tmp`
            - `-rwsr-xr-x.  1 root root 30768 2月  22 2012 /usr/bin/passwd`
    - 解析
        - `s`
            - User
                - SUID，暂时得到root权限且仅用于二进制文档
                    - /usr/bin/passwd 程序允许用户更新自己密码
                    - 但该程序会存取/etc/shadow密码文件，该文件却权为root所占有
            - Group
                - SGID（Set GID）
                - 档案
                    - SGID 是设定在 binary file 上面，则不论使用者是谁，在执行该程序的时候， 他的有效群组 (effective group) 将会变成该程序的群组所有人 (group id)。
                - 目录
                    - SGID 是设定在 A 目录上面，则在该 A 目录内所建立的档案或目录的 group ，将会是 此 A 目录的 group
            - 其中如目录不具备x权限，但强制设置了s，则显示权限时会显示大写的S表示为空。
                - 下面 `t/T`的显示逻辑与之相同。
        - `t`
            - Other
            - SBit（Sticky Bit）设定于目录
            - 在具有 SBit 的目录下，使用者若在该目录下具有 w 及 x 的权限
            - 当使用者在该目录下建立档案或目录时，只有档案拥有者与 root 才有权力删除
    - 设置
        - `shattr abcd file/dir`
            - `a=[SUID=4, SGID=2, SBit=1]`
​

- 档案类型
    - `file file/dir`
        - 显示档案的基本类型
        - 示例
            - `file hello.java `
                - `hello.java: ASCII C++ program text`
            - `file hello.class `
                - `hello.class: compiled Java class data, version 51.0`
            - `file zh_cn.txt `
                - `zh_cn.txt: ISO-8859 text`
            - `file ../resource/`
                - `../resource/: directory`

- 档案搜索
    - `which [-a] `
    - `whereis [-bmsu] file/dir`
    - `locate file/dir`
        - 搜索自建数据库/var/lib/slocate/，且仅每天建立一次预设
        - 更新 `/etc/updatedb.conf` 中 **DAILY_UPDATE** 进行默认建立
    - `find [PATH] [option] [action]`
        - 时间相关
            - `-atime/ctime/mtime n`
                - n天前**一天之内被访问/改变/修改**过的档案
            - `-newer file`
                - 比指定文件新的文件等均会被列出
        - 用户群组相关
            - `-uid/gid n`
                - 指定用户或群组ID
            - `-user/group`
                - 指定用户或群组名称
            - `-nouser/-nogroup`
                - 寻找档案拥有者不存在于/etc/passwd或/etc/group中的
        - 档案权限及名称相关
            - `-name filename`
                - 搜寻文件名称为 filename 的档案
                - 示例
                    - `find -name "slide-rId3*"`
                        - 寻找前缀为 `slide-rId3` 的文件
            - `-size [+-]SIZE`
                - 搜寻比 SIZE 还要大(+)或小(-)的档案
                - SIZE 的规格
                    - c 代表 byte
                    - k 代表 1024bytes
                - 示例
                    - `find -size +50k`
                        - 找比 50KB 还要大的档案
            - `-type TYPE`
                - 搜寻档案的类型为 TYPE 的
                - 类型
                    - `f`
                        - 一般正规档案
                    - `b, c`
                        - 装置档案
                    - `d`
                        - 目录
                    - `l`
                        - 连结档
                    - `s`
                        - socket
                    - `p`
                        - FIFO
            - `-perm mode`
                - 搜寻档案属性『刚好等于』 mode 的档案，这个 mode 为类似 chmod的属性值，举例来说， -rwsr-xr-x 的属性为 4755 ！
            - `-perm -mode`
                - 搜寻指定属性的档案
                - 示例
                    - `ls -lh | grep "\-rw\-r\-\-r\-\-"`
                        ```
                        -rw-r--r-- 1 appuser appuser     2 May  2 11:24 chattr-test.dat
                        -rw-r--r-- 1 appuser appuser  1.5M Jan 18 12:08 from.gif
                        -rw-r--r-- 1 appuser appuser   70K Sep  1  2017 no-optimize-slide-rId2.jpg
                        -rw-r--r-- 1 appuser appuser   74K Sep 21  2017 slide-rId2.jpg
                        -rw-r--r-- 1 appuser appuser   83K Sep 21  2017 slide-rId3.jpg
                        -rw-r--r-- 1 appuser appuser  118K Sep 21  2017 slide-rId4.jpg
                        -rw-r--r-- 1 appuser appuser   32K Oct 19  2017 watermark-basic-blue.png
                        ```
                    - `find -perm 644`
                        ```
                        ./from.gif
                        ./slide-rId3.jpg
                        ./no-optimize-slide-rId2.jpg
                        ./slide-rId2.jpg
                        ./callback/callback-save-to-zip.zip
                        ./watermark-basic-blue.png
                        ./chattr-test.dat
                        ./audio/audio.mp3
                        ./slide-rId4.jpg
                        ```
            - `-perm +mode`
                - 搜寻档案属性『包含任一 mode 的属性』的档案，举例来说，我们搜寻-rwxr-xr-x ，亦即 -perm +755 时，但一个档案属性为 -rw-------也会被列出来，因为他有 -rw.... 的属性存在！
        - 额外操作
            - `-exec command`
                - command 为其它指令，-exec 后面可再接额外的指令来处理搜寻到的结果。
            - `-print`
                - 将结果打印到屏幕上，这个动作是预设动作！
    - 比较
        - whereis/locate（速度**快**，利用数据库，不搜索硬盘）**>** find（速度**慢**，操作硬盘）
    - 事例
        - `find / -perm +7000 -exec ls -l {} \;`
            - `{}` 代表的是由 `find` 找到的内容        
            - `\;` 表示 `exec` 指令结束


#### 档案压缩和打包
- 用途与技术
    - 实现思路
        - 1 `byte`= 8 `bits`
            - 相关于每个`byte`中有8个空格并可选0/1
        - 考虑到1表示时为 `0000 0001`，相关于7bit为空，压缩便是**利用算法用上这些剩余的位置**

- 常见指令        
    - 常见格式
        - `*.Z compress`
            - 程序**压缩**的档案；
        - `*.bz2 bzip2`
            - 程序**压缩**的档案；
        - `*.gz gzip`
            - 程序**压缩**的档案；
        - `*.tar tar`
            - 程序**打包**的数据，并没有压缩过；
        - `*.tar.gz tar`
            - 程序**打包**的档案，其中并且经过 **gzip** 的压缩
​
- 常见指令
    - `compress [-dcr] file/dir [for -c: toFile/toDir]`
        - 压缩后原文件会被压缩文件替换
        - 参数
            - `-d`
                - 用来解压缩的参数，或可直接使用`uncompress`
            - `-r`
                - 可以连同目录下的档案也同时给予压缩呢！
            - `-c`
                - 将压缩数据输出成为 standard output (输出到屏幕)，
                    - `compress -c man.config > man.config.back.Z`

    - `gzip [-cdt#] fileName [for -c: toFileName]`
        - 参数
            - `-c`
                - 将压缩的数据输出到屏幕上，可透过数据流重导向来处理
            - `-d`
                - 解压缩的参数
            - `-t`
                - 可以用来检验一个压缩档的一致性,看看档案有无错误
            - `-#`
                - **压缩等级**
                - `-1` 最快，但是压缩比最差、`-9` 最慢，但是压缩比最好
                - 预设是 -6
    - `gunzip [-acfhlLnNqrtvV][-s ][file...]`

    - `zcat fileName.gz`
        - 用于读取压缩文件数据内容

    - `bzip2 [-cdz#] fileName [for -c: toFileName]`
    - `bzcat fileName.bz2`
    - `tar [-cxtzjvfpPN] file|dir..`
        - 参数
            - `-c`
                - 建立一个压缩档案的参数指令(create 的意思)；
            - `-x`
                - 解开一个压缩档案的参数指令！
            - `-t`
                - 查看 tarfile 里面的档案
                - `c/x/t` 仅能存在一个！不可同时存在！因为不可能同时压缩与解压缩。
            - `-z`
                - 是否同时具有 gzip 的属性？亦即是否需要用 gzip 压缩？
            - `-j`
                - 是否同时具有 bzip2 的属性？亦即是否需要用 bzip2 压缩？
            - `-v`
                - 压缩的过程中显示档案！这个常用，但不建议用在背景执行过程！
            - `-f`
                - 使用档名，请留意，在 f 之后要立即接档名喔！不要再加参数！例如使用『 tar -zcvfP tfile sfile』就是错误的写法，要写成『 tar -zcvPf tfile sfile』才对喔！
            - `-p`
                - 使用原档案的原来属性（属性不会依据使用者而变）
            - `-P`
                - 可以使用绝对路径来压缩！
            - `-N`
                - 比后面接的日期(yyyy/mm/dd)还要新的才会被打包进新建的档案中！
            - `--exclude FILE`
                - 在压缩的过程中，不要将 FILE 打包
    - 示例
        - `tar -cvf all.tar bzip2.txt compress.txt gzip.text`       
            - 将指定文件**打包**
        - `tar -czvf all.tar.gz bzip2.txt compress.txt gzip.text`   
            - 将指定文件打包并以gzip方式压缩
        - `tar -tzvf all.tar.gz` 
            - 将指定文件**拆包**
            ```
            -rw-r--r-- root/root       229 2014-09-29 11:28 bzip2.txt
            -rw-r--r-- root/root       229 2014-09-29 10:00 compress.txt
            -rw-r--r-- root/root       229 2014-09-29 10:06 gzip.txt
            ```
        - `tar --exclude /home/dmtsai -zcvf myfile.tar.gz /home/* /etc`     
            - 备份 `/home`, `/etc` ，但不要 `/home/dmtsai`

    - `dd if="input_file" of="outptu_file" bs="block_size" count="number"`     
        - 主要用于大文件的备份还原
            - 分区
            - 磁盘
            - Used盘
        - 参数
            - `if` 
                - input file
            - `of` 
                - output file
            - `bs` 
                - 规划的一个 block 的大小
                - 默认 512 bytes
            - `count` 
                - 多少个 bs 的意思。
        - 示例
            - ` dd if=test.gif of=test.gif.iso`

    - `cpio -covB > [file|device]`
        - **备份**
    - `cpio -icduv < [file|device]`
        - **还原**
        - 参数
            - `-o`
                - 将数据 copy 输出到档案或装置上
            - `-i`
                - 将数据自档案或装置 copy 出来系统当中
            - `-t`
                - 查看 cpio 建立的档案或装置的内容
            - `-c`
                - 一种较新的 portable format 方式储存
            - `-v`
                - 让储存的过程中文件名称可以在屏幕上显示
            - `-B`
                - 让预设的 Blocks 可以增加至 5120 bytes
                - 预设是 512 bytes ！这样的好处是可以让大档案的储存速度加快(请参考 i-nodes 的观念)
            - `-d`
                - 自动建立目录！由于 cpio 的内容可能不是在同一个目录内，如此的话在反备份的过程会有问题
                - 这个时候加上 -d 的话，就可以自动的将需要的目录建立起来了
            - `-u`
                - 自动的将较新的档案覆盖较旧的档案！
    - 示例
        - `find / -print | cpio -covB > /dev/st0`
            - 将所有系统上的数据通通写入磁带机内
        - `cpio -icdvt < /dev/st0 > /tmp/content`
            - 查看磁带机内容，并将输出文件名纪录至 `/tmp/content`



# vi文字处理器
## 功能介绍
- Linux 与 Unix 系统中的参数文件几乎都是 `ASCII` 码的纯文字文件
    - 纯文字文件= 以 ASCII 格式码为主
    - 不论使用什么编辑器均可正常使用

## vi使用
### 模式分类
- 一般模式
    - 默认进入模式

- 编辑模式
    - 等到您按下『i, I, o, O, a, A, r, R』等字母之后才会进入编辑模式
    - 按「ESC」退出编辑模式

- 指令列命令模式
    - 在一般模式当中，输入 `:` 或 `/` 或 `?`就可以进入该模式
    - 在这个模式当中，可达成 搜寻、读取、存盘、大量取代字符、离开 vi 、显示行号 等动作

### 补充操作
- `wq!`
    - 权限不对时强制写入
- `[n] h/j/k/l`
    - 光标向左/下/上/右方向移动n个字符
- `0/$/H/L/[n]G/[n]Enter`
    - 移动至行首/行尾/页首/页尾/第n行/向下移动n行
- `/word或?word`
    - 向下或向上搜索word字符串
- `n/N`
    - 重复前一搜索操作或反向搜索
- `[n]x/X`
    - 向后/前删除n/1个字符
- `[n]dd`
    - 删除游标向下n/1行
​
### 一般模式
#### 移动光标
- `h | ←`
    - 光标向左移动一个字符
- `j | ↓`    
- `k | ↑`    
- `l | →`    
    - 多次移动,例如向下移动 30 行
        - `30j`
        - `30↓`
- n<space>            
    - 输入 `n` 空格
- `0`                   
    - 移动到**行首**
- `$`                   
    - 移动到**行尾**
- `H`                   
    - 移动屏幕中首行
- `M`                   
    - 移动到屏幕中间行
- `L`                   
    - 移动屏幕末行
- `G`                   
    - 移动**末行**
- `nG`                  
    - 移动到**第 n 行**
- `gg`                  
    - 移动**首行**
    - 等价于 `1G`
- `n<Enter>`            
    - 光标**向下移动 n 行**


#### 搜寻与取代
- `/word`               
    - 向光标之下寻找一个字符串名称为 word 的字符串
- `?word`               
    - 向光标之上寻找一个字符串名称为 word 的字符串。
- `n`                   
    - 重复前一个搜寻的动作
- `N`
    - 为『反向』进行前一个搜寻动作

#### 删除、复制与贴上
- `dG`
    - 删除光标所在到最后一行的所有数据
- `d$`
    - 删除游标所在处到该行的最后一个字符
- `d0`
    - 删除游标所在处到该行的最前面一个字符
- `yy`
    - **复制当前行**
- `nyy`
    - 复制光标所在的向下 n 行
        - `20yy` 
            - 复制 20 行
- `y1G`
    - 复制光标所在列到第一列的所有数据
- `yG`
    - 复制光标所在列到最后一列的所有数据
- `y0`
    - 复制光标所在的那个字符到该行行首的所有数据
- `y$`
    - 复制光标所在的那个字符到该行行尾的所有数据
- `p, P`
    - p 为将已复制的数据在**光标下一行**贴上，P 则为贴在游标上一行
    - 示例
        - 目前光标在第 20 行，且已经复制了 10 行数据。
            - 按下 p 后， 那 10 行数据会贴在原本的 20 行之后，亦即由 21 行开始贴。
            - 按下 P 呢？ 那么原本的第 20 行会被推到变成 30 行。
- `J`                   
    - 将光标所在列与下一列的数据结合成同一列
- `c`                   
    - 重复删除多个数据
    - 示例
        - `10cj`
            - 向下删除 10 行
- `u`                   
    - **复原**前一个动作
- `[Ctrl]+R`         
    - **重做**上一个动作

#### 指令列命令模式
- `:w`                  
    - 将编辑的数据写入硬盘档案中
- `:w!`                 
    - 若档案属性为只读时，**强制写入**该档案。
- `:q`                  
    - 离开 vi
- `:q!`                 
    - 若曾修改过档案，又不想储存，使用 `!` 为**强制退出不储存**档案。
- `:wq`                 
    - 储存后离开，若为 :wq! 则为**强制储存后退出** 
- `:e!`                 
    - 将档案**还原到最原始的状态**
- `ZZ`                  
    - 若档案没有更动，则不储存离开，若档案已经经过更动，则储存后离开！
- `:w [filename]`       
    - 将编辑的数据储**另存**成一个档案
- `:r [filename]`       
    - 在编辑的数据中，读入另一个档案的数据
- `:n1,n2 w [filename]` 
    - 将 n1 到 n2 的内容储存成 filename 这个档案
- `:! command`          
    - 暂时离开 vi 到指令列模式下执行 `command` 的显示结果
    - 示例
        - `:! ls /home`
            - 可在 vi 当中察看 /home 底下以 ls 输出的档案信息！
- `:set nu`             
    - **显示行号**
- `:set nonu`           
    - **取消行号显示**


## vim额外功能

### 区块选择- 多选
#### 常用指令
- `v`   
    - 字符选择，会将光标经过的地方反白选择
- `V`   
    - 行选择，会将光标经过的行反白选择
- `[Ctrl]+v`
    - 区块选择，可以用长方形的方式选择资料
- `y`   
    - 将反白的地方复制起来
- `d`   
    - 将反白的地方删除掉
- `P`   
    - 将复制内容粘贴


### 多档案编辑
#### 操作指令
- `vim file1...`   
    - 批量打开多个文档
    - 参数
        - `:n`      
            - 编辑下一档案
        - `:N`      
            - 编辑上一档案
        - `:files`  
            - 罗列此次打开的所有文件

### 多窗口编辑
#### 操作指令
- `vim file1`
    - 参数
        - `:sp file2`
            - 将file2加入窗口编辑
        - `[ctrl]+wj/wk`    
            - 移到下方/上方窗口

### vim环境设定
- `vi ~/.vimrc`
    - `:set hlsearch`       
        - 设置搜寻字符串反白
    - `:set backspace=2`    
        - 2时使用 `backspace` 可删除任意字符，为0或1时仅可删除刚输入字符
    - `:set autoindent`     
        - 自动缩排设置
    - `:set ruler`          
        - 设置右下角状态列说明
    - `:set showmode`       
        - 显示状态列，如 `--INSERT--`
    - `:syntax on`          
        - 根据语法显示不同颜色并主动排错


## 断行区别
### 区别
- `DOS`
    - 回车换行/ CR LF/ ^M$
- `Linux`
    - 换行/ LF/ $ 
### 查看
- `cat -A file`
### 转换
- `dos2unix/unix2dos [-kn] file [newFile]`
    - 参数
        - `-k`
            - 保留该档案原本的 mtime 时间格式 (不更新档案上次内容经过修订的时间)
        - `-n`
            - 保留原本的旧档，将转换后的内容输出到新档案，如： dos2unix -n old new



# BASH SHELL
## 功能介绍
### 概念
    - 硬件核心 
        - kernel
    - 与核心交互工具
        - shell
    - bash
        - Bourne Again shell，常见shell版本
    - shell
        - [ Shell[ 核心-kernel[ 硬件]]]
    - 查询系统支持shell版本
        - ll /bin/*sh


### BASH功能
    - 命令编修
        - 自动记录使用过的指令，其中~/.bash_history存储着前一次登入所执行的指令【有黑客入侵查看到数据库登录等的操作】
    - 命令补全
        - 输入指令前面部分字符后按[tab]进行自动补全，未自动补全双击[tab]查看所有可执行指令
    - 命令别名
        - 为命令串自订短命令，alias la='ls -al'，后续直接使用la查看所有的文件（含隐藏文件）
    - 工作|前景|背景控制
    - shell scripts
    - 万用字符（*）
​
### 内建指令
- `type [-tpa] command`
    - 参数
        - `-t`
            - 当加入 -t 参数时，type 会将 `command` 以底下这些字眼显示出他的意义：
            - `file`
                - 表示为外部指令
            - `alias`
                - 表示该指令为命令别名所设定的名称
            - `builtin`
                - 表示该指令为 bash 内建的指令功能
        - `-p`
            - 如果后面接的 `command` 为指令时，会显示完整文件名(外部指令)或显示为内建指令；
        - `-a`
            - 会将由 `PATH` 变量定义的路径中，将所有含有 `command` 的指令都列出来，包含 `alias`
​
### 变量功能
- `yao=yao`     
    - 设置变量值
        - 变量与变量内容以`=`来连结
        - 等号两边**不能直接接空格符**
        - 变量名称只能是**英文字母与数字**，但是数字不能是开头字符
        - 若有空格符可以使用双引号 `"` 或 `'` 来将变量内容结合起来
            - **双引号**内的特殊字符可以**保有变量**特性
            - **单引号**内的特殊字符则仅为**一般字符**
        - 必要时需要以跳脱字符 `\` 来将特殊符号 (如 `Enter`,` $`, `\`,` 空格符`,`'` 等) 变成一般符号；
        - 由其它的指令提供的信息，可使用 `` `command` ``
        - 扩增变量内容
            - `"$PATH":/home』` 以追加内容
        - 将变量变成环境变量
            - `export PATH`
        - 建议
            - 大写字符为系统预设变量
            - 自行设定变量可以使用小写字符

- `echo $yao`   
    - 输出变量值
- `unset yao`   
    - 取消变量
- ``quote(`)``
    - 复合指令
    - ``ls -l `locate crontab` ``  
        - 优先执行``中的指令，并将其执行结果作为外部的输入信息
​
### 变量用途
#### 应用场景
    - 简化名称 
    - 批量进行名称替换（scripts中）


## 环境变量

- `env`
    - 列出目前的 shell 环境下的所有环境变量与其内容
- `set`
    - 显示仅于当前SHELL环境下可用的环境变量及其相关内容
- `PS1`
    - 提示字符设定
    - 参数
        - `\d`
            - 代表日期，格式为 Weekday Month Date，例如 "Mon Aug 1"
        - `\H`
            - 完整的主机名称。举例来说，鸟哥的练习机 linux.dmtsai.tw ，那么这个主机名称就是 linux.dmtsai.tw
        - `\h`
            - 仅取主机名称的第一个名字。以上述来讲，就是 linux 而已， .dmtsai.tw 被省略。
        - `\t`
            - 显示时间，为 24 小时格式，如： HH:MM:SS
        - `\T`
            - 显示时间，12 小时的时间格式！
        - `\A`
            - 显示时间，24 小时格式， HH:MM
        - `\u`
            - 目前使用者的账号名称；
        - `\v`
            - BASH 的版本信息；
        - `\w`
            - 完整的工作目录名称。家目录会以 ~ 取代；
        - `\W`
            - 利用 basename 取得工作目录名称，所以仅会列出最后一个目录名。
        - `\#`
            - 下达的第几个指令。
        - `\$`
            - 提示字符，如果是 root 时，提示字符为 # ，否则就是 $ 啰～
    - 示例
        - `PS1='[\u@\h \d \t \w]'`
            - `[root@localhost 四 10月 23 18:39:26 /data/test]`

- `$$`
    - 本shell的PID
- `$?`
    - 上一指令的回传值，其中成功执行时为0，否则返回对应错误码
- `OSTYPE/HOSTTYPE/MACHTYPE`
    - 主机硬件与核心等级
    - 示例
    - `echo $HOSTTYPE`  
        - `x86_64`
    - `echo $OSTYPE`    
        - `linux-gnu`
    - `echo $MACHTYPE`  
        - `x86_64-redhat-linux-gnu`

- `export`
    - 显示所有的环境变量
- `locale`
    - 语系档案变量
    - 示例
        - `locale -a`
            - 显示所支持的语系

- `read/array/declare`
    - `read [-pt] variable`
        - 参数：
            - `-p`
                - 接提示字符
            - `-t`
                - 等待秒数
        - 事例
            - `read -p "Please keyin your name: " -t 30 named`
                - 提示使用者 30 秒内输入自己的大名，将该输入字符串做成 named 变量
            - `echo $named`
                - 输出使用者输入信息
    - `declare [-aixr] variable`
        - 参数：
            - `-a`
                - 将后面的 variable 定义成为数组 (array)
            - `-i`
                - 将后面接的 variable 定义成为整数数字 (integer)
            - `-x`
                - 用法与 export 一样，就是将后面的 variable 变成环境变量；
            - `-r`
                - 将一个 variable 的变量设定成为 readonly ，该变量不可被更改内容，也不能 unset
        - 事例：
            - `yao=handsome.guy~`
            - `declare -x yao`
            - `declare -r yao`
            - `unset yao/ yao=1`
                - `-bash: yao: readonly variable`
    - `array`
        - 事例
            - `[root@localhost targz]# var[1]=yao`
            - `[root@localhost targz]# var[2]=qing`
            - `[root@localhost targz]# var[3]=ju`
            - `[root@localhost targz]# echo "${var[1]} ${var[2]} ${var[3]}"`
                - `yao qing ju`


- `ulimit [-SHacdflmnpstuv] [配额]`
    - 限制使用者的系统资源
    - 参数
        - `-H`
            - hard limit
            - 严格的设定，必定不能超过设定的值
        - `-S`
            - soft limit
            - 警告的设定，可超但会有警告讯息
        - `-a`
            - 列出所有的限制额度
        - `-c`
            - 可建立的最大核心档案容量 (core files)
        - `-d`
            - 程序数据可使用的最大容量
        - `-f`
            - 此 shell 可以建立的最大档案容量 (一般可能设定为 2GB)单位为 Kbytes
        - `-l`
            - 可用于锁定 (lock) 的内存量
        - `-p`
            - 可用以管线处理 (pipe) 的数量
        - `-t`
            - 可使用的最大 CPU 时间 (单位为秒)
        - `-u`
            - 单一使用者可以使用的最大程序(process)数量。

​
- 命令别名设定
    - alias
        - 设置别名
        - 事例
            - `alias la='ls -al'`
                - 罗列所有别名0
                    -  `alias cp='cp -i'`
                    -  `alias l.='ls -d .* --color=auto'`
                    -  `alias la='ls -al'`
                    -  `alias ll='ls -l --color=auto'`
                    -  `alias ls='ls --color=auto'`
                    -  `alias mv='mv -i'`
                    -  `alias rm='rm -i'`
                    -  `alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'`

- `history [n|-c|[-raw] histfiles]`
    - 显示本次登录所**操作过所有指令**
    - 参数
        - `n`
            - 列出最近的 n 笔命令列表
        - `-c`
            - 内容全部**消除**
        - `-a`
            - **新增写入** histfiles，默认为 `~/.bash_history`
        - `-r`
            - **读取** histfiles
        - `-w`
            - **写入** histfiles
    - `!66`
        - 执行第66条记录
    - `!al`
        - 执行以al开头的命令
​
- `/etc/issue`
    - 本机**登录**时显示的提示信息
    - 参数
        - `\d`
            - 本地端时间的日期
        - `\l`
            - 显示第几个终端机接口
        - `\m`
            - 显示硬件的等级 (i386/i486/i586/i686...)
        - `\n`
            - 显示主机的网络名称
        - `\o`
            - 显示 domain name
        - `\r`
            - 操作系统的版本 (相当于 uname -r) 
        - `\t`
            - 显示本地端时间的时间
        - `\s`
            - 操作系统的名称
        - `\v`
            - 操作系统的版本
    - 事例
        - 文件内容
            ```
            CentOS release 6.3 (Final)
            Kernel \r on an \m
            ```
        - 显示内容
            ```
            CentOS release 6.3 (Final)
            Kernel 2.6.32-279.e16.x86_64 on an x86_64
            ```

- `/etc/motd`
    - 客户端登录后提示信息，内容为文本实际内容


### 额外功能

#### 变量有效范围
    - 当启动一个 shell ，操作系统分配一记忆区块给 shell 使用，此区域之变量可以让子程序存取
    - 利用 export 功能，可以让变量的内容写到上述的记忆区块当中(环境变量)
    - 当加载另一个 shell 时 (亦即启动子程序，而离开原本的父程序了)，子 shell 可以将父 shell 的环境变量所在的记忆区块导入自己的环境变量区块当中

#### 信息读取
- `param=/data/test/download/targz/redis-2.8.3/src/hahaha.jar`
    - `echo ${param##/*/}`
        - `hahaha.jar`
        - 两个`#`表示截取掉两个`/`中间的所有值，以**最长**长度优先
    - `echo ${param#/*/}` 
        - `test/download/targz/redis-2.8.3/src/hahaha.jar`
        - 单个`#`表示截取掉两个`/`中间的所有值，以**最短**长度优先
    - `echo ${param%%/*}` 
        - ` `
        - 两个`%`表示截取掉`/`后的所有值，以**最长**长度优先
    - `echo ${param%/*}`  
        - `/data/test/download/targz/redis-2.8.3/src`
        - 单个`%`表示截取掉`/`后的所有值，以**最短**长度优先
- `param=/data/test/download/targz/redis-2.8.3/src/test.jar`
    - `echo ${param/test/woca}`
        - `/data/woca/download/targz/redis-2.8.3/src/test.jar`
        - 替换第一个test字符串
    - `echo ${param//test/woca}`
        - `/data/woca/download/targz/redis-2.8.3/src/woca.jar`
        - 替换所有的test字符串
​
#### 变量设定

| 变量设定方式     | str 没有设定       | str 为空字符串     | str 已设定非为空字符串 |
|:-----------------|:-------------------|:-------------------|:-----------------------|
| var=${str-expr}  | var=expr           | var=               | var=$str               |
| var=${str:-expr} | var=expr           | var=expr           | var=$str               |
| var=${str+expr}  | var=expr           | var=expr           | var=expr               |
| var=${str:+expr} | var=expr           | var=               | var=expr               |
| var=${str=expr}  | str=expr var=expr  | str 不变 var=      | str 不变 var=$str      |
| var=${str:=expr} | str=expr var=expr  | str=expr var=expr  | str 不变 var=$str      |
| var=${str?expr}  | expr 输出至 stderr | var=               | var=str                |
| var=${str:?expr} | expr 输出至 stderr | expr 输出至 stderr | var=str                |


#### 环境设定档
- 系统设定值
    - `/etc/sysconfig/i18n`
        - **语言**配置
    - `/etc/bashrc`
        - 每一shell用户**登录**时读取的**配置文件**
    - `/etc/profile.d/*.sh`
        - 针对bash及Cshell的**规范数据**
    - `/etc/man.config`
        - 指定man指令时对应的**man page**路径
- 个人设定值
    - `~/.bash_profile`
        - 个人设定文档，登录时读取，定义个人化的路径和环境变量
    - `~/.bash_login, ~/.profile`
        - 同.bash_profile，但shell启动时优先读bash_profile，找不到时找login，最后才是profile
    - `~/.bashrc`
        - 执行shell script时读取，建议存放命令别人和路径等
    - `~/.bash_history`
        - 用户操作指令记录
    - `~/.bash_logout`

- 读取顺序
    - `/etc/profile `
    - `[/etc/profile.d | /etc/inputrc] `
    - `[~/.bash_profile | ~/.bash_login | ~/.profile] `
    - `~/.bashrc`

#### 终端机的环境设定
- `stty`
    - 参数
        - -a ：将目前所有的 stty 参数列出来
    - 事例
        - stty -a
            ```
            speed 38400 baud; rows 71; columns 237; line = 0;
            intr = ^C; quit = ^\; erase = ^?; kill = ^U; eof = ^D; eol = <undef>; eol2 = <undef>; swtch = <undef>; start = ^Q; stop = ^S; susp = ^Z; rprnt = ^R; werase = ^W; lnext = ^V; flush = ^O; min = 1; time = 0;
            -parenb -parodd cs8 -hupcl -cstopb cread -clocal -crtscts -cdtrdsr
            -ignbrk -brkint -ignpar -parmrk -inpck -istrip -inlcr -igncr icrnl ixon -ixoff -iuclc -ixany -imaxbel - -iutf8
            opost -olcuc -ocrnl onlcr -onocr -onlret -ofill -ofdel nl0 cr0 tab0 bs0 vt0 ff0
            isig icanon iexten echo echoe echok -echonl -noflsh -xcase -tostop -echoprt echoctl echoke
            ```
        - `stty erase ^h`
            - 使用 `ctrl+ h` 进行回退
- `set`
    - 参数
        - `-u`
            - 预设**不启用**
            - 若启用后，当使用未设定变量时，会显示错误讯息
        - `-v`
            - 预设**不启用**
            - 若启用后，在讯息被输出前，会先显示讯息的原始内容
        - `-x`
            - 预设**不启用**
            - 若启用后，在指令被执行前，会显示指令内容(前面有 ++ 符号)
        - `-h`
            - 预设**启用**
            - 与历史命令有关
        - `-H`
            - 预设**启用**
            - 与历史命令有关
        - `-m`
            - 预设**启用**
            - 与工作管理有关
        - `-B`
            - 预设**启用**
            - 与刮号 [] 的作用有关
        - `-C`
            - 预设**不启用**
            - 若使用 `>` 等，则若档案存在时，该档案不会被覆盖
    - 事例
        - `echo $-`
            - 显示当前所有set设置，如 `himBH`



 
# 异常及处理
## `-bash: make: command not found`
- 原因
    - 安装为最小化版本，未安装make/vim等常用命令
- 处理
    - 直接于控制台执行 yum -y install gcc automake autoconf libtool make

# 应用实例
## 服务重启
### Tomcat部署WAR
- 使用CRT连接linux
- `cd /usr/local/bin/`    
    - Linux系统软件安装目录
- `pwd`
    - 显示当前目录名    
- `cd ~`
    - 进入当前用户目录
- `cd ..`
    - 返回至上级目录 
- `cd /home/` 
    - 返回至/ `home/`目录
- `cd apache-tomcat-8.0.9/webapps`
- `rm -rf ops*`
    - 不提示确认框，递归地删除ops开头的文件
- `rm`    
    - 删除
- `-r`     
    - 递归
- `-f`     
    - 不提示确认
- `o*`    
    - 以o开头>rz并选择要上传的文件
    - 其中rz为CRT自带工具，Linux中也需安装相应功能
- `cd ..`
- `cd bin`
- `./shutdown.sh`
    - `./`
        - 执行本文件夹中的可执行文件
- `./startup.sh`

### Mule服务重启

- `/usr/local/bin/mule-standalone-3.5.0/bin`
- `./mule [start|stop|restart]`
    - 进行mule项目的开启、关闭或重启操作

### 关机步骤

- `sync`
    - 将数据同步写入硬盘
    - `Root` 角色下使用，保证内存中的缓存数据及时写入硬盘
- `shutdown`
    - 惯用关机指令
- `reboot,halt,poweroff`
    - 重新开机、关机
    - 于关机前均会调用 `sync` 指令，但为保证数据准备，建议还是自行再次调用 `sync` 指令保护数据


### VI操作

#### 以开发Java程序为例
- `vi Hello.java`
- `i`
    >
        public class Hello{
            public static void main(String[] args){
                System.out.println("Hello World!");
            }
        }

- `ESC`
    - 进入命令模式
- `wq`
    - 保存后退出同连按两次大写字母Z，输入 q! 则表示只退出不保存
- `javac Hello.java`
- `java Hello`

#### 开发C++程序为例
- vi Hello.cpp
- `i`
    >
        #include<stdio.h>
        int main(){
            printf("Hello World!");
            return 0;
        }
- `gcc Hello.cpp`
    - 用ls查看编译后文件名，默认为a.out
- `./a.out`