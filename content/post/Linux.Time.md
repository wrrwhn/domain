---
title: "Linux.Time"
date: "2018-07-05"
categories:
 - "整理"
tags:
 - "Linux"
toc: true
---


# Command
## Function
- 测量指定指令执行所消耗的时间、系统资源

## Format
- `time [options] <command [arguments...]>`

## result
```sh
/usr/bin/time -p ls -l 
total 2104
-rw-rw-r-- 1 appuser appuser     218 Aug 14  2017 curl-cookie-test
real 0.00
user 0.00
sys 0.00
```

## options
- option

    | 选项                                 | 描述                                               |
    |--------------------------------------|----------------------------------------------------|
    | -f `<FORMAT>`<br>--format=`<FORMAT>` | **指定输出格式**<br>重写环境变量 `TIME` 指定的模式 |
    | -p<br> --portability                 | 使用 `POSIX` 的默认格式                            |
    | -o `<FILE>`<br> --output=`<FILE>`    | 将结果 **重定向**至指定文件                        |
    | -a<br> --append                      | 结合 `-o <FILE>` 使用；表示以追加形式写入           |
    | -v<br> --verbose                     | 输出 **详细**结果                                  |
    | --help                               | 打印帮助文档                                       |
    | -V<br> --version                     | 打印版本信息                                       |

- option.format

    | 类型 | 选项 | 描述                                                                                                           |
    |------|------|----------------------------------------------------------------------------------------------------------------|
    | 时间 |      |                                                                                                                |
    |      | %E   | **消耗**时间，格式为 `[hours:]minutes:seconds`                                                                  |
    |      | %e   | **实时运行**用时，以秒为单位                                                                                    |
    |      | %S   | System Time<br>**内核**运行的总体 **CPU 时间**，以秒为单位                                                                     |
    |      | %U   | User Time<br>**用户**模式运行下的总体 **CPU 时间**，以秒为单位                                                               |
    |      | %P   | 当前任务所占 **CPU 百分比**<br>公式为 `(%U + %S) / %E`                                                         |
    | 内存 |      |                                                                                                                |
    |      | %M   | 进程生命周期内的 **最大驻留大小**，单位为 KB                                                                    |
    |      | %t   | 进程 **平均驻留大小**，单位为 KB                                                                                |
    |      | %K   | 进程 **平均总内存**使用情况，单位为 KB； 公式为 `数据+ 堆栈+ 文本`                                               |
    |      | %D   | 进程的 **非共享**数据区域大小，单位为 KB                                                                        |
    |      | %p   | 进程的 **非共享**堆栈空间 **平均值**，单位为 KB                                                                 |
    |      | %X   | 进程的 **共享文本**空间的 **平均值**，单位为 KB                                                                 |
    |      | %Z   | 系统的 **页面大小**，以字节为单位<br> **各系统预设常量值不同**                                                  |
    |      | %F   | 进程运行时发生 **错误**的主 **页面**（必须由硬盘中读取）**数量**                                                 |
    |      | %R   | **小、可恢复的页面错误的数量**，特指其它虚拟页面未声明但无效的错误；因此页面数据仍然有效，但系统页面分配表需要更新 |
    |      | %W   | 进程被 **调换出主内存的次数**                                                                                  |
    |      | %c   | 进程被动进行 **上下文切换的次数**<br>**时间片过期的情况**                                                      |
    |      | %w   | 进程主动进行 **上下文切换后的等待时长**<br>例如 **等待 I/O**操作的完成                                         |
    | I/O  |      |                                                                                                                |
    |      | %I   | 进程中进行文件 **输入的数量**                                                                                  |
    |      | %O   | 进程中进行文件 **输出的数量**                                                                                  |
    |      | %r   | 进程 **接收 socket 消息的数量**                                                                                |
    |      | %s   | 进程 **发送的 socket 消息的数量**                                                                              |
    |      | %k   | 提交给进程的 **信号数量**                                                                                      |
    |      | %C   | 正在被講的命令的名称和命令行参数                                                                               |
    |      | %x   | 命令行的 **退出状态码**                                                                                        |

    - option.format.exit_status
    
        | 类型 | 状态值 | 描述             |
        |------|--------|------------------|
        | 成功 |        |                  |
        |      | 0      | 正常结束         |
        | 失败 |        |                  |
        |      | 64     | 命令行使用错误   |
        |      | 65     | 数据格式错误     |
        |      | 66     | 无法打开输入流   |
        |      | 67     | 地址未知         |
        |      | 68     | 主机名未知       |
        |      | 69     | 服务不可用       |
        |      | 70     | 内部软件错误     |
        |      | 71     | 系统错误         |
        |      | 72     | 关键系统文件缺失 |
        |      | 73     | 无法创建输出文件 |
        |      | 74     | 输入输出错误     |
        |      | 75     | 重试<br>临时错误 |
        |      | 76     | 远程协议错误     |
        |      | 77     | 权限不足         |
        |      | 78     | 配置错误         |
        |      | 126    | 命令无法执行     |
        |      | 127    | 命令未找到       |

## command
- 正常调用指定，如 ll
    - 但需要使用完整路径
        - `ll` 需使用 `ls -l`
        - 调用文件时，使用完整路径名


# Example
- `/usr/bin/time -v ps -aux`
    - 显示指令的完整信息
    
        ```sh
        USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
        root         2  0.0  0.0      0     0 ?        S     2017   0:06 [kthreadd]

            Command being timed: "ps -aux"
            User time (seconds): 0.00
            System time (seconds): 0.01
            Percent of CPU this job got: 78%
            Elapsed (wall clock) time (h:mm:ss or m:ss): 0:00.01
            Average shared text size (kbytes): 0
            Average unshared data size (kbytes): 0
            Average stack size (kbytes): 0
            Average total size (kbytes): 0
            Maximum resident set size (kbytes): 1736
            Average resident set size (kbytes): 0
            Major (requiring I/O) page faults: 0
            Minor (reclaiming a frame) page faults: 532
            Voluntary context switches: 2
            Involuntary context switches: 240
            Swaps: 0
            File system inputs: 0
            File system outputs: 0
            Socket messages sent: 0
            Socket messages received: 0
            Signals delivered: 0
            Page size (bytes): 4096
            Exit status: 0
        ```

- `/usr/bin/time -f "\ntime: %E"  /usr/local/ffmpeg -i /data/090913f7-b3e3-4050-ba7b-a9d7d664fd8d/record.m3u8 -c copy -bsf:a aac_adtstoasc -y /data/090913f7-b3e3-4050-ba7b-a9d7d664fd8d/090913f7-v2.mp4`
    - 定制显示数据格式

        ```sh
        [mp4 @ 0x291f020] Non-monotonous DTS in output stream 0:0; previous: 26142599, current: 24141600; changing to 26142600. This may result in incorrect timestamps in the output file.
        frame=12746 fps=2334 q=-1.0 Lsize=   27848kB time=00:04:50.71 bitrate= 784.7kbits/s speed=53.2x    
        video:25851kB audio:1731kB subtitle:0kB other streams:0kB global headers:1kB muxing overhead: 0.966411%
        time: 0:05.51
        ```

# Supplement 
- 页面错误
	- 由系统硬件引起的异常类型，当进程访问内存页面，MMU 未映射到进程的虚拟地址空间

- MMU
	- Memory Management Unit 
	- 内存管理单元


# Reference
## 官方
- [time.main](http://otzm88f21.bkt.clouddn.com/e1d9551c-7186-49cd-af3b-9ef8487510b7.main)
    - `man time`

## 参考
- [Page fault](https://en.wikipedia.org/wiki/Page_fault)
- [Linux命令详解 — time](https://blog.csdn.net/thinkerABC/article/details/647272)
- [shell 程序 返回码 退出码](http://blog.51cto.com/jackwxh/827600)
- [sysexits.h](https://opensource.apple.com/source/Libc/Libc-320/include/sysexits.h)