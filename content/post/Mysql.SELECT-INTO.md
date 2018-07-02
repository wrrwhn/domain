---
title: "Mysql.SELECT-INTO"
date: "2017-08-15"
categories:
 - "整理"
tags:
 - "Mysql"
toc: true
---


## 参考
- [SELECT ... INTO Syntax](https://dev.mysql.com/doc/refman/5.7/en/select-into.html)
- [MySQL下SELECT…INTO OUTFILE导出文本文件命令](http://www.wzxue.com/mysql%E4%B8%8Bselect-into-outfile%E5%AF%BC%E5%87%BA%E6%96%87%E6%9C%AC%E6%96%87%E4%BB%B6%E5%91%BD%E4%BB%A4/)
- [mysql导入数据load data infile用法](http://hunan.iteye.com/blog/752606)
- [SELECT INTO OUTFILE](https://mariadb.com/kb/en/the-mariadb-library/select-into-outfile/)

## 命令
- `SELECT ... INTO `
    - `VAR_LIST`
        - 将栏目值存储至`变量`
        - 栏目值需与变量名数据一致
        - 仅返回一条记录
            - 无数据返回，则报 Error（1329）
            - 多行记录返回，则报 Error（1172）
            - 建议使用 LIMIT 1
    - `OUTFILE`
        - 将选择栏目值存储至`文件`
        - 存储于服务器，需持有文件权限
        - 文件路径不能为已存在文件
            - 客户端执行 `mysql -e "SELECT ..." > file_name ` 导出文件
        - 文件格式使用情况
            - .txt
                - 数据会以 TAB 间隔
            - csv
                - csv格式，但数据均存储至同一单元格内
    - `DUMPFILE`
        - 将`单行`数据以`任意形式`文件导出

- OUTPUT FORMAT
    - FIELDS
        - `TERMINATED BY` '分隔符，默认 `\t`'
        - `ENCLOSED BY` '字段括起字段，默认 `\`'
        - `ESCAPED BY` '转义字符'
    - OPTIONALLY
        - `ENCLOSED BY` ''
    - LINES
        - `TERMINATED BY` '默认 `\n`'


## 实例
- VAR_LIST
    ```
    SELECT id, data INTO @x, @y FROM test.t1 LIMIT 1;
    ```

- OUTFILE
    - 查看权限目录
        ```
        mysql> show variables like 'datadir';
        +---------------+-------------------+
        | Variable_name | Value             |
        +---------------+-------------------+
        | datadir       | /data/mysql/data/ |
        +---------------+-------------------+
        ```
    - 导出数据
        ```
        select e.id, e.name, q.id, q.content, o.content, o.true_flag, u.user_name, if(a.option_id is not null, if(a.option_id= o.id, o.content, ''), a.reply) as reply
        INTO OUTFILE '/data/mysql/tmp/2758.txt'
        from question_options o
            inner join question q on o.question_id= q.id and q.deleted= 0
            inner join exercises e on q.exercises_id= e.id and e.rel_id= 906 and e.rel_type = 2 and e.type = 0 and e.used= 1
            left join question_answer a on q.id= a.question_id
            left join user u on a.user_id = u.id
        order by e.id, q.id, a.user_id;
        ```
    - 格式化导出数据
        ```
        select e.id, q.id, u.user_name, if(a.option_id is not null, o.content , a.reply) as reply
            INTO OUTFILE '/data/mysql/data/2758.txt'
                COLUMNS ENCLOSED BY '|'
                LINES STARTING BY '|' TERMINATED BY '|\n'
        from question_answer a
            inner join user u on a.user_id = u.id
            left join question_options o on a.option_id= o.id and o.deleted= 0
            inner join question q on a.question_id= q.id and q.deleted= 0
            inner join exercises e on q.exercises_id= e.id and e.deleted= 0 and e.id in (12506,12507,12508,12509,12525,12523)
        order by e.id, q.id, a.user_id;
        ```


## 补充
### 导入数据
- 语句
    ```
    load data infile '/tmp/t0.txt'
    ignore into table t0
        character set gbk
        fields terminated by ','
            enclosed by '"'
        lines terminated by '\n'
    (`name`,`age`,`description`);
    ```
- 指令
    - `character set gbk `
        - 必输，避免乱码
    - `ignore into table`
        - 比如 `name` 列加了唯一索引，可避免重复插入报错
