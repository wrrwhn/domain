---
title: "Linux.AWK"
date: "2018-07-11"
categories:
 - "整理"
tags:
 - "Linux"
toc: true
---


# Description
## AWK
- 1977年，贝尔实验室开发的文本处理神器
- 命名取自其创始人Alfred Aho、Peter Weinberger 和 Brian Kernighan 的姓的首字母
- 推荐书籍
    《The AWK Programming Language》
## GAWK
- GNU Project 的 AWK 解释器

# Command
## Format
- `awk [options] [-f program-file|program-text] file...`
- `awk 'BEGIN{ <commands> } pattern{ <commands> } END{ <commands> }'`
    - 执行 BEGIN
    - 逐行扫描匹配 PATTERN
    - 执行 END

## Options


| 类型     | 变量                                       | 含义                                                                                                              |
|----------|--------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| 常用     |                                            |                                                                                                                   |
|          | -F <fs><br>--field-separator <fs>          | 指定字段分隔符                                                                                                    |
|          | -v <key>=<value><br>--assign <key>=<value> | 设置变量值，在 BEGIN 模块开始执行前执行                                                                            |
|          | -E <file><br>--exec <file>                 | 类似 `-f`，针对于脚本、 CGI 应用<br>将禁用命令行变量赋值                                                            |
|          | -e <code-block><br>--source <code-block>   | 在命令行中执行源代码                                                                                              |
| 文件     |                                            |                                                                                                                   |
|          | -d[file]<br>--dump-variables[=file]        | 将全局变量打印至文件<br>默认打印至 `awkvars.out`                                                                  |
|          | -p <prof_file><br>--profile <prof_file>    | 保存性能分析数据<br>默认为 awkprof.out                                                                            |
|          | -g<br>--gen-pot                            | 描述并解析 AWK 程序至 GNU .pot（Portable Object Template） 文件中                                                   |
|          | -V<br>--version                            | 打印详细的版本信息                                                                                                |
| 可选     |                                            |                                                                                                                   |
|          | -O<br>--optimize                           | 启用优化                                                                                                          |
|          | -h<br>--help                               | 打印帮助文档                                                                                                      |
|          | -P<br>--posix                              | 开始兼容模式<br> `\x`不被解析                                                                                     |
|          | -n<br>--non-decimal-data                   | 识别数据中的八进制、十六进制数据<br>谨慎使用                                                                       |
|          | -b<br>--characters-as-bytes                | 将所有输入均当成单字节字符                                                                                        |
|          | -t<br>--lint-old                           | 针对不可移植于 Unix AWK 的结构的警告                                                                              |
|          | -L [value]<br>--lint[=value]               | 针对可疑、不可移植的结构代码提供警告                                                                               |
|          | -S<br>--sandbox                            | 开启沙盒模式<br>使用 system() 方法，使用 getline 进行输入，使用 print[f] 进行输出<br>有效地阻止脚本对本地资源的访问 |
|          | -N<br>--use-lc-numeric                     | 解析输入数据时，强制 GAWK 使用本地十进制的点                                                                       |
| 版本相关 |                                            |                                                                                                                   |
|          | -c<br>--traditional                        | 兼容模式，不使用 GAWK 扩展                                                                                         |
|          | -r<br>--re-interval                        | 允许在正则表达式中使用区间表达式<br>默认允许，需配合 `--traditional` 使用                                          |
|          | -C<br>--copyright                          | 打印简短的 GNU 版权信息                                                                                           |
|          | -R<br>--command file                       | 从文件中读取命令<br> 仅 DGAWK 可用                                                                                |

## Built-in Variables

| 类型 | 变量值      | 含义                                          |
|------|-------------|-----------------------------------------------|
| 常用 |             |                                               |
|      | $0          | 当前记录行                                    |
|      | $1~$n       | 当前记录的第n个字段，字段间由FS分隔            |
|      | FS          | 输入字段分隔符<br>默认为空格或Tab             |
|      | RS          | 输入的记录分隔符<br>默认为换行符              |
|      | OFS         | 输出字段间分隔符<br>默认为空格                |
|      | ORS         | 输出行间分隔符<br>默认为换行符                |
| 补充 |             |                                               |
|      | ENVIRON     | 当前环境属性拷贝列表，使用如 `ENVIRON["HOME"]` |
|      | NF          | 当前记录行的列数                              |
|      | NR          | 已读记录行数，从1开始<br>跨多文件              |
|      | FNR         | 当前文件的已读记录行数                        |
|      | FILENAME    | 当前输入文件的名字                            |
|      | OFMT        | 输出数据格式<br>默认为`%.6g`                  |
|      | RT          | 输出行分隔符<br>默认同 RS                     |
|      | ARGC        | 命令行参数的数目                              |
|      | ARGIND      | 命令行中当前文件的位置(从0开始算)             |
|      | ARGV        | 包含命令行参数的数组                          |
|      | CONVFMT     | 数字转换格式<br>默认为`%.6g`                  |
|      | ERRNO       | 最后一个系统错误的描述                        |
|      | FIELDWIDTHS | 字段宽度列表<br>用空格键分隔                  |
|      | IGNORECASE  | 忽略大小写的匹配                              |
|      | RLENGTH     | 由 match 函数所匹配的字符串的长度             |
|      | RSTART      | 由 match 函数所匹配的字符串的第一个位置       |
|      | SUBSEP      | 数组下标分隔符<br>默认为 `/034`               |


## Printf.*

| 类型 |  变量  |                                含义                                |
|------|--------|--------------------------------------------------------------------|
| 基础 |        |                                                                    |
|      | %c     | 字符<br>若输入数字，则转为对应字符<br>若输入为文本，则仅输出首字符 |
|      | %d, %i | 小数，整数                                                         |
|      | %e, %E | `[-]d.dddddde[+-]dd` 形式浮点数                                    |
|      | %f, %F | `[-]ddd.dddddd` 形式浮点数                                         |
|      | %g, %G | 彩 `%e` 和 `%f` 形式数据中短者                                     |
|      | %o     | 无符号八进制数                                                     |
|      | %u     | 无符号十进制数                                                     |
|      | %s     | 字符文本                                                           |
|      | %x, %X | 无符号十六进制数<br> `%X` 则将使用大写字母显示                     |
|      | %%     | `%`                                                                |
| 补充 |        |                                                                    |
|      | -      | 左对齐                                                             |
|      | space  | 数值转换时，正值无符号，负数补`-`                                  |
|      | +      | 于宽度修饰符前使用，正数时补`+`                                    |
|      | #      | 修饰符，如八进制前补`0`，十六进制前补`0x`                          |
|      | 0      | 数值显示时，值小于字段宽度时，补0                                  |
|      | width  | 字段填充至指定宽度                                                 |
|      | .prec  | 指定数值的小数位精度                                               |


## *.Functions
- 详见 [Reference/ Man](#Man)
    - Numeric
    - String
    - Time
    - Bit Manipulations
    - Type
    - Internationalization

# Example
## Data

- cat netstat.log
    ```sh
    Active Internet connections (w/o servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State      
    tcp        1      0 localhost:59796         localhos:macromedia-fcs CLOSE_WAIT 
    tcp        0      0 localhost:55125         localhost:mysql         ESTABLISHED
    tcp        1      0 localhost:59184         localhos:macromedia-fcs CLOSE_WAIT 
    tcp        0      0 iZ94nvigjtdZ:scp-config iZ94nvigjtdZ:35778      ESTABLISHED
    tcp        0 720149 localhost:47634         localhost:20100         FIN_WAIT1  
    ```

- [netstat.log](http://otzm88f21.bkt.clouddn.com/38c169bc-4859-4d43-9d3e-66984ad0abc0.log)


## Show

- `awk '{print $1, $6}' netstat.log`

    ```sh
    Active 
    Proto Foreign
    tcp CLOSE_WAIT
    tcp ESTABLISHED
    tcp CLOSE_WAIT
    tcp ESTABLISHED
    tcp FIN_WAIT1
    ```


## Format
- `awk '{printf "%-8s %-22s\n", $1, $6}' netstat.log`

    ```sh
    Active                         
    Proto    Foreign               
    tcp      CLOSE_WAIT            
    tcp      ESTABLISHED           
    tcp      CLOSE_WAIT            
    tcp      ESTABLISHED           
    tcp      FIN_WAIT1   
    ```


## Filter
- `awk '$2>0 && $6=="ESTABLISHED" && $5 ~ /47400|42785/ || NR== 2 {printf "%+d\t%20s\t%-20s\n",$2,$5,$6}' netstat.log  | less`

    ```sh
    +0                   Address    Foreign             
    +205414      localhost:47400    ESTABLISHED         
    +223733      localhost:42785    ESTABLISHED  
    ```


## File
- `awk 'NR!= 1 { if($6 ~ /FIN_WAIT|ESTABLISHED|CLOSE_WAIT/) printf "%s\n", $0 > $6;}' ../netstat.log`


    ```sh
    -rw-rw-r-- 1 appuser appuser  5840 Jul 11 16:31 CLOSE_WAIT
    -rw-rw-r-- 1 appuser appuser 41840 Jul 11 16:31 ESTABLISHED
    -rw-rw-r-- 1 appuser appuser  3280 Jul 11 16:31 FIN_WAIT1
    -rw-rw-r-- 1 appuser appuser   480 Jul 11 16:31 FIN_WAIT2
    ```

- `awk -f sum.awk ../netstat.log`
    - `cat sum.awk`

        ```sh
        #!/bin/awk -f
        BEGIN{
                wait= 0

                printf "TYPE            NUM\n"
                printf "-------------------\n"
        }
        {
                wait+=$6 ~ /WAIT/? 1: 0
                printf "%10s\t%d\n", $6, ($6 ~ /WAIT/? 1: 0)
        }
        END{
                printf "--------------------\n"
                printf "\tWAIT.NUM:%10d\n\tWAIT.AVG: %10f\n",wait, wait/NR
        }
        ```

    - result

        ```sh
        TYPE            NUM
        -------------------
                        0
        Foreign      0
        CLOSE_WAIT      1
        ESTABLISHED     0
        ....
        CONNECTED      0
        --------------------
                WAIT.NUM:       136
                WAIT.AVG:   0.176853
        ```


# Reference
## Man
- [gawk.log](http://otzm88f21.bkt.clouddn.com/e7effdb5-b4b9-4042-9480-54d16d093c39.log)

## More
- [AWK 简明教程](https://coolshell.cn/articles/9070.html)
- [linux awk命令详解](https://www.cnblogs.com/ggjucheng/archive/2013/01/13/2858470.html)
- [awk、nawk、mawk、gawk的简答介绍](http://blog.sina.com.cn/s/blog_3d2d79aa0100h47h.html)
- [Linux awk 命令](http://www.runoob.com/linux/linux-comm-awk.html)
- [awk命令](http://man.linuxde.net/awk)