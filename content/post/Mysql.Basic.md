---
title: "Mysql.Basic"
date: "2016-12-10"
categories:
 - "整理"
tags:
 - "Mysql"
toc: true
---

# 基础知识
## 存储结构
```
表空间：所有数据逻辑地存放于ib_data1文件中
    |- 数据库
        |- 区：InnoDB每次最多申请4个区，即 4M 的存储空间
            |- 页1：B-Tree 结构存储，16KB，大小不能调整，Mysql 最小逻辑单位
            |- ……
            |- 页* 64
                |- 行1： 数据行存在数据行中，每页最多7992行
                |- ……
                |- 行* 7992
    |- 索引段
    |- 回滚段
    |- 页
注：InnoDB按每张表的主键构造一 B+ 树
```
## 索引
```
聚集索引
    是否支持聚集引擎，取决于采用的存储引擎，其中InnoDB会建立。
    概念：实际数据行和相关的键值保存在一起。
    原理：把索引和数据都保存于一 B+ 树数据结构，并同时将索引列与相关数据行保存在一起。则访问同一数据页不同记录，数值数据于内存中已存在。
    注：一表仅可包含一个聚集索引，仅于按索引顺序过滤查询有效。
非聚集索引
    概念& 原理：参考聚集索引，同样按索引值进行 B+ 树存储，但区别于树中只存放索引和相应数值的指针，而不与数据一同存储。
联合索引
    概念：两个或两个以上列索引
    注意：建立时考虑列顺序，索引越少越好（更新数据时需维护索引值）
注：避免file sort（索引不到情况下使用临时文件排序查找）、临时表（建立在系统临时文件夹中的表）和表扫描（操作中数据库引擎必须读取表中的所有页以查找符合查询条件的行）。
```

## 执行顺序
```
（5）SELECT DISTINCT  TOP()
（1）FROM (1-J) <left_table> <join_type> JOIN <right_table> ON <on_predicate>
| (1-A) <left_table> <apply_type> APPLY <right_table_expression> AS <alias>
| (1-P) <left_table> PIVOT(<pivot_specification>) AS <alias>
| (1-U) <left_table> UNPIVOT(<unpivot_specification>) AS <alias>
（2）WHERE <where_predicate>
（3）GROUP BY <group_by_specification>
（4）HAVING <having_predicate>
（6）ORDER BY <order_id_list>
```

# 操作相关
## 更改密码
```
mysql -u root -p
> Enter password
user mysql;
update user set password=passworD("new pass") where user='root';
flush privileges;
exit;
```

## 允许指定 IP 访问
```
> mysql -uroot -p;
>  Yk1qazxsw2    // root

> use mysql;
 
> show grants for yunkai_user@110.89.71.200;
 
> GRANT ALL PRIVILEGES ON yunkai.* TO 'yunkai_user'@'110.89.71.200' IDENTIFIED BY '@Y#z!2aZx';
> GRANT ALL PRIVILEGES ON yunkai.* TO 'yunkai_user'@'120.36.189.%' IDENTIFIED BY '@Y#z!2aZx';    // 范围值
```
