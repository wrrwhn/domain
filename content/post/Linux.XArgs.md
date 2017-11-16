+++
date = "2017-10-16T22:07:46+08:00"
title = "Linux.XArgs"
draft = false
tags = ["整理","Linux"]
share = true
+++

# 参考
- [Linux 系統 xargs 指令範例與教學](https://blog.gtwang.org/linux/xargs-command-examples-in-linux-unix/)
- [Wiki-xargs](https://zh.wikipedia.org/wiki/Xargs)


# 作用
- 将参数列表转换成小块分段传递给其他命令，以**避免参数列表过长**
- 以空白字符或换行作为分隔，将标准输入切割为多字符串并**作为指定指令执行时的参数**
    - INPUT
        - `xargs cat`
        - `arg1 arg2`
        - `arg3`
    - EXECUTE
        - `cat arg1 arg2 arg3`

# 参数
- `-d`
    - 指定输入的分隔符
    - `-d\n`
- ``
    - 指定每次执行时所用**参数上限值**
    - `echo a b c d e f | xargs -n 3`
        - `echo a b c`
        - `echo d e f`
- `-p`
    - 每条分割语句执行前**需经确认**
    - `echo 1 2 3 | xargs -p`
        - `echo 1 2 3 ?...`
            - `y`
- `-r`
    - 避免以空字符串作为参数
    - `echo | xargs -p -r`
- `-t`
    - 显示执行的指令
    - `echo 1 2 3 | xargs -t`
        - `echo 1 2 3 `
        - `1 2 3`


# 示例
- 删除当前目录下，指定格式的重复图片
    - `find . -name "*).png" | xargs -p rm -f`
