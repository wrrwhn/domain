+++
date = "2017-02-26T22:07:46+08:00"
title = "MYSQL.Information_schema"
draft = false
tags = ["整理","MYSQL"]
share = true
+++


[TOC]


# MYSQL.INFORMATION_SCHEMA
## 简介
- MySQL自带的，它提供了访问数据库元数据的方式。
- 元数据是关于数据的数据，如数据库名或表名，列的数据类型，或访问权限等。
- 保存着关于MySQL服务器所维护的所有其他数据库的信息。如数据库名，数据库的表，表栏的数据类型与访问权限等。

## 库表
- SCHEMATA
    - 提供了当前mysql实例中所有数据库的信息。是show databases的结果取之此表。
- TABLES
    - 提供了关于数据库中的表的信息（包括视图）。详细表述了某个表属于哪个schema，表类型，表引擎，创建时间等信息。是show tables from schemaname的结果取之此表。
- COLUMNS
    - 提供了表中的列信息。详细表述了某张表的所有列以及每个列的信息。是show columns from schemaname.tablename的结果取之此表。
- STATISTICS
    - 提供了关于表索引的信息。是show index from schemaname.tablename的结果取之此表。
- USER_PRIVILEGES（用户权限）
    - 给出了关于全程权限的信息。该信息源自mysql.user授权表。是非标准表。
- SCHEMA_PRIVILEGES（方案权限）
    - 给出了关于方案（数据库）权限的信息。该信息来自mysql.db授权表。是非标准表。
- TABLE_PRIVILEGES（表权限）
    - 给出了关于表权限的信息。该信息源自mysql.tables_priv授权表。是非标准表。
- COLUMN_PRIVILEGES（列权限）
    - 给出了关于列权限的信息。该信息源自mysql.columns_priv授权表。是非标准表。
- CHARACTER_SETS（字符集）
    - 提供了mysql实例可用字符集的信息。是SHOW CHARACTER SET结果集取之此表。
- COLLATIONS
    - 提供了关于各字符集的对照信息。
- COLLATION_CHARACTER_SET_APPLICABILITY
    - 指明了可用于校对的字符集。这些列等效于SHOW COLLATION的前两个显示字段。
- TABLE_CONSTRAINTS
    - 描述了存在约束的表。以及表的约束类型。
- KEY_COLUMN_USAGE
    - 描述了具有约束的键列。
- ROUTINES
    - 提供了关于存储子程序（存储程序和函数）的信息。此时，ROUTINES表不包含自定义函数（UDF）。名为“mysql.proc name”的列指明了对应于INFORMATION_SCHEMA.ROUTINES表的mysql.proc表列。
- VIEWS
    - 给出了关于数据库中的视图的信息。需要有show views权限，否则无法查看视图信息。
- TRIGGERS
    - 提供了关于触发程序的信息。必须有super权限才能查看该表

- INNODB_BUFFER_PAGE
    - 提供了缓冲区中每页相关的元数据信息，包括更新缓冲索引页和更新缓冲位图页
- INNODB_BUFFER_PAGE_LRU
    - 提供缓冲池中各页的消息，特别用于当缓冲池满时通过 LRU（近期最少使用算法）裁定移除哪些界面
- INNODB_BUFFER_POOL_STATS
    - 提供缓冲池各页状态信息，通过标识 young 和 not young 状态来判定该各页的回收时机
- INNODB_CMP
    - 提供压缩 InnoDB 表的操作状态信息
- INNODB_CMPMEM
    - 提供压缩缓冲池中页面的操作状态信息
- INNODB_CMP_PER_INDEX
    - 提供压缩表和索引的操作状态信息，并进行单独统计各种库、表、索引的组织，帮助评估特定表压缩的效果和作用
- INNODB_FT_BEING_DELETED
    - 为 INNODB_FT_DELETED 的快照，仅用于执行 OPTIMIZE TABLE 时使用。当 OPTIMIZE TABLE 运行时时， INNODB_FT_BEING_DELETED 会被清空，并从 INNODB_FT_DELETED 中将 DOC_IDs 移除出来
    - DOC_ID
        - 执行删除操作时，该行的文档 ID
        - 该值指向基本表定义时的 ID 列
- INNODB_FT_CONFIG
    - 展示全文索引和表关联执行的元数据
    - 查询前，为包含有全文索引的表设置  innodb_ft_aux_table 配置属性
- INNODB_FT_DEFAULT_STOPWORD
    - 创建全文索引时默认配置的停用词列表
- INNODB_FT_DELETED
    - 记录由全文索引中被删除的记录行
- INNODB_FT_INDEX_CACHE
    - 包含在全文索引中新插入行的 token 信息
- INNODB_FT_INDEX_TABLE
    - 展示执行文本搜索时的倒序实现的相关信息
- INNODB_LOCKS
    - 包含各已被请求但未被提交的事务锁，或被其它事务所阻塞的当前事务锁的信息
- INNODB_LOCK_WAITS
    - 包含各阻塞事务的一至多行记录信息，用于描述锁如何被请求但又被其它锁所阻塞
- INNODB_METRICS
    - 获取计数器的相关启动、停止和重启的相关信息
- INNODB_SYS_COLUMNS
    - 提供表栏目的元数据信息，等价于 SYS_COLUMNS 中的信息
- INNODB_SYS_DATAFILES
    - 提供表空间的数据文件的路径信息，等价于 SYS_DATAFILES 中的信息
- INNODB_SYS_FIELDS
    - 提供索引关联字段的元数据，等价于 SYS_FIELDS 中的信息
- INNODB_SYS_FOREIGN
    - 提供外键的元数据，等价于 SYS_FOREIGN 中的信息
- INNODB_SYS_FOREIGN_COLS
    - 提供外键相关字段名的状态信息
- INNODB_SYS_INDEXES
    - 提供索引的元数据，等价于 SYS_INDEXES 中的信息
- INNODB_SYS_TABLES
    - 提供库表的元数据，等价于 SYS_TABLES 中的信息
- INNODB_SYS_TABLESPACES
    - 提供 File-Per-Table 和一般表空间的元数据，等价于 SYS_TABLESPACES 中的信息
    - File-Per-Table 主要用于提升成功恢复的机会和节省时间，进行表复制、备份时的状态报告
- INNODB_SYS_TABLESTATS
    - 提供库表的低等级状态信息，该数据用于在表查询时优化计算以判定使用哪个索引
- INNODB_SYS_VIRTUAL
    - 提供虚拟生成字段或其所信赖的栏目信息的元数据，等价于 SYS_VIRTUAL 中的信息
    - [generated virtual columns](https://dev.mysql.com/doc/refman/5.7/en/glossary.html#glos_generated_virtual_column)
        + 栏目值通过栏目定义的表达式计算而来
- INNODB_TEMP_TABLE_INFO
    - 提供当前状态的临时表的元数据，除了内部优化的临时表
    - 记录所有用户和系统创建，激活状态，在指定实例中的临时表。
    - 临时表仅保存于内存中，并不保存到硬盘
- INNODB_TRX
    - 包含每个当前正在执行事务的信息，包括当事务开始时、执行 sql 时的事务是否正在等待锁


## 参考
[INFORMATION_SCHEMA Tables](https://dev.mysql.com/doc/refman/5.7/en/information-schema.html)
[InnoDB INFORMATION_SCHEMA Tables](https://dev.mysql.com/doc/refman/5.7/en/innodb-i_s-tables.html)
