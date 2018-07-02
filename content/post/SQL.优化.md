---
title: "SQL.优化"
date: "2018-04-27"
categories:
 - "整理"
tags:
 - "SQL"
toc: true
---


# 特点
- SQL为非过程化语言，只需要告诉机器做什么，而不需要指名每一步该怎么做。
- SQL语句的输入输出均为同一类型数据对象（记录集），因此可以轻松实现嵌套功能。


# 优化
## TOP-N问题
- table
    - `student{sid, sname, sage}`
- sql
    ``` sql
    select sid, sname, sage from (
        select rownum temp_no, sid, sname, sage from(
            select sid, sname, sage from student
            order by sid
        )
    )
    where temp_no between 4 and 6;
    ```
 
## 恰当使用索引
- 创建
    - 简单索引= 指定字段值 + ROWID
        - 隐藏字表，而ROWID相当于目录当中的页码
- 影响
    - select 查找的字段有索引，加快查找速度
    - insert 则会减速
    - delete/update 则加快和减慢的可能性都存在
 
## 用case表达式替代多次查询
- 普通查询
    - select count(*) "below 25" from student where sage <= 25;
    - select count(*) "between 25 and 30" from student where sage >25 and sage <30;
    - select count(*) "above 30" from student where sage >=30;
- case实现
    ``` sql
    select
        count(case when sage<=25 then 1 else null end) "below 25",
        count(case when sage>25 and sage<30 then 1 else null end) "between 25 and 30",
        count(case when sage>30 then 1 else null end) "above 30",
    from student;
    ```
 
## 用where子句代替having子句
```
select * from table 
    group by num having num=1;
    where num=1 group by num;
```
 
## 多表开发注意事项
- 正确连接方式
    - table

        | Table | Rows |
        |-------|------|
        | t1    | 1000 |
        | t2    |   10 |
        | t3    |  100 |

    - 顺序
        - **t2连接到t3，而后将t3连接到t1**
- 尽量使用全称列名
    - 避免花费时间决定某一列属于哪个表


# 示例
## 列转行
- table
    - material{id（编号）/type（类型）/num（数量）}

- sql
    ```
    select
        --substr用于从指定字段的指定位置截取指定个数的字符
        substr(id, 1, 3) || '-' ||substr(id, 4, 3) || '-' || sbustr(id, 7, 3) "ID",
        --decode函数用于吧字段的值按照不同的情况翻译成其他形式的输出，类似于java的switch语句
        decode(type, 'A', TO_CHAR(num, '99999'), '') "甲类型",
        --TO_CHAR函数将输入转化为指定格式的文本
        decode(type, 'B', TO_CHAR(num, '99999'), '') "乙类型",                                                    
        decode(type, 'C', TO_CHAR(num, '99999'), '') "丙类型",
    from material;
    ```
                
## 关联更新
- table
    - author{aid（作者编号）/aname（作者姓名）/acountbooks（数量）}
    - work_book{aid（作者编号）/wbname（书籍名称）}

- sql
    ```
    update author aa
        set acountbooks =(
            select bs from (
            -- 内层嵌套将对应作者著书数量检索出来的sql语句
                select count(*) bs, aid naid from work_book wa
                -- 统计每个作者数量的sql语句
                group by wa.aid                                                                                
                union
                --work_book表中不存在的作者的书籍统计量为0
                select 0, ab.aid from author ab                                                
                where ab.aid not in(
                    --检索出同一作者书籍情况
                    select wc.aid from work_book wc                                            
                    group by wc.aid
                )
            )
            where naid = aa.aid
        );
    ```
                
 