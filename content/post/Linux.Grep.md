---
title: "Linux.Grep"
date: "2017-09-20"
categories:
 - "整理"
tags:
 - "Linux"
toc: true
---


# Grep
## 参考
- [grep命令](http://man.linuxde.net/grep)
- [每天一个linux命令（39）：grep 命令](http://www.cnblogs.com/peida/archive/2012/12/17/2821195.html)
- [linux grep命令](http://www.cnblogs.com/end/archive/2012/02/21/2360965.html)
- [man-grep](http://otzm88f21.bkt.clouddn.com/b9a82f1e-a3ba-4f54-b6e4-7aa00de43296.txt)


## 格式
- `grep [OPTIONS] PATTERN [FILE...]`
- `grep [OPTIONS] [-e PATTERN | -f FILE] [FILE...]`

## 参数
### 匹配器选择
- `-E, --extended-regexp`
    - 扩展正则表达式
    - 由 POSIX 指定
- `-F, --fixed-strings, --fixed-regexp`
    - 模式解析为固定字符串列表，以换行符分隔，任一匹配
    - *弃用别名*
- `-G, --basic-regexp`
    - 解析为基本正则表达式
    - 默认配置
- `-P, --perl-regexp`
    - 模式解析为 Perl 正则表达式
    - 开发阶段，会提示未实现的特性

### 匹配控制
- `-e PATTERN, --regexp=PATTERN`
    - 文本正则
- `-f FILE, --file=FILE`
    - 正则条件文本文件，以换行符分隔
    - 空文件则匹配不到任何东西
- `-i, --ignore-case`
    - 忽略正则表达式和内容的字符大小写匹配限制
- `-v, --invert-match`
    - 反向匹配
    - 输出正则匹配到的元素之外的信息
- `-w, --word-regexp`
    - 全字匹配
- `-x, --line-regexp`
    - 全行匹配
- `-y`
    - `-i` 的弃用同义词

### 控制输出   
- `-c, --count`
    - 输出匹配的各文件中的行数
- `--color[=WHEN], --colour[=WHEN]`
    - 在终端以颜色区分匹配字符串，以转义字符显示包围的相关分隔符等信息
    - 默认根据系统参数 `GREP_COLORS` 显示，但目前已弃用，优先级降低
- `-L, --files-without-match`
    - 打印出每个输入文件名称
    - 终止于第一条匹配数据
- `-l, --files-with-matches`
    - 同 `-L`
    - 由 POSIX 指定
- `-m NUM, --max-count=NUM`
    - 最多输出 `NUM` 个匹配项
- `-o, --only-matching`
    - 仅打印匹配行，以换行符分隔
- `-q, --quiet, --silent`
    - 不打印标准输出
    - 成功时以 0 状态退出，失败时以 非0 值退出
- `-s, --no-messages`
    - 不打印异常消息

### 输出前缀控制   
- `-b, --byte-offset`
    - 每行输出，0字节偏移
- `-H, --with-filename`
    - 打印每个匹配的文件名
- `-h, --no-filename`
    - 输出时忽略文件前缀
    - 默认
- `--label=LABEL`
    - 以实际标准输入作为 <LABEL> 文件的输入
    - `gzip -cd foo.gz | grep --label=foo -H`
- `-n, --line-number`
    - 显示行号
- `-T, --initial-tab`
    - 缩进对齐
- `-u, --unix-byte-offsets`
    - 以 Unix 风格的字节偏移
- `-Z, --null`
    - 以0字节分隔文件名和匹配项

### 上下文控制   
- `-A NUM, --after-context=NUM`
    - 显示匹配行后指定数量行数额外信息
- `-B NUM, --before-context=NUM`
    - 显示匹配行前指定数量行数额外信息
- `-C NUM, -NUM, --context=NUM`
    - 输出匹配行前后指定数量行额外信息，各匹配行之间以空行间隔
- `--group-separator=SEP`
    - 使用指定符号作为组分隔符
    - 默认为 `--`
- `-no-group-separator`
    - 使用空字符串作为组分隔符

### 文件目录选项   
- `-a, --text`
    - 执行文本类型的二进制文件
    - 等价于 `--binary-files=text`
- `--binary-files=TYPE`
    - 指定文件类型匹配
    - 默认为 `binary`
- `-D ACTION, --devices=ACTION`
    - 当输入为文件为设备、FIFO 或流时，使用指定动作<ACTION>处理
    - 默认为 `read`，可选项为 `skip`
- `-d ACTION, --directories=ACTION`
    - 当输入文件为文档时，使用指定<ACTION>处理
    - 默认为 `read`，可选项为 `recurse`
        - `recurse`
            - 递归读取每个目录，其中符号连接符仅在命令行中指定时查询
            - 等价于 `-r`
- `--exclude=GLOB`
    - 忽略文件名匹配<GLOB>的文件
    - 可使用 `* ? .` 作为通配符
- `--exclude-from=FILE`
    - 忽略文件名匹配<FILE>中任一规则的文件
- `--exclude-dir=DIR`
    - 递归搜索中，排除匹配正则的目录
- `-I`
    - 处理二进制文件，如同未包含匹配数据
    - 等价于 `--binary-files=without-match`
- `--include=GLOB`
    - 仅搜索文件名匹配<GLOB>的文件
- `-r, --recursive`
    - 递归搜索所有目录
    - 符号链接仅于命令行中指定时生效
    - `-d`

### 其它选项   
- `--line-buffered`
    - 在输出使用行缓冲，会导致性能损失
- `-U, --binary`
    - 将文件当成二进制文件
    - MS-DOS 和 MS-WINDOWS 下，会自动抓取前 32KB 内容以判断文件类型
- `-z, --null-data`
    - 将输入视为一组行，每行以0字节代替换行符作为分隔符

## 示例
- `grep "match_pattern" file_name --color=auto`
    - 标记匹配颜色
- `echo this is a test line. | egrep -o "[a-z]+\."`
    - 仅输出匹配内容
- `grep "text" . -r -n`
    - 递归当前目录进行搜索
- `echo this is a text line | grep -e "is" -e "line" -o`
    - 多匹配条件
- `grep "main()" . -r --include *.{php,html}`
    - 递归并指定 .php 和 .html 文件进行搜索
- `grep -q "test" filename`
    - 静默执行，仅返回执行状态
        - 0 为成功
        - 其它为失败
- `seq 10 | grep "5" -A 3 -B 2`
    - 显示匹配记录后三行和前两行记录
- `seq 10 | grep "5" -C 1`
    - 显示匹配记录的前后各一行记录
