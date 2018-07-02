---
title: "Oracle.基础"
date: "2016-12-10"
categories:
 - "整理"
tags:
 - "Oracle"
toc: true
---


## 系统构造
### The System Global Area (SGA)
- 组成                  描述
- Buffer Cache          作为查询修改前的优先查看区
- Shared Pool           SQL重用、储存程序、数据字典信息如用户账号信息及表索引
- Redo Log Buffer       多次缓存重做信息一次写入
- Large Pool            服务进程的大数据量I/O请求之用
- Java Pool             用于Java虚拟机的特定会话的代码和数据
- Streams Pool          Oracle流产品专用
### The Program Global Area (PGA)
- 系统启动后应客户端请求建立的服务进程专用的，主要用于实现SQL和保存登陆等相关会话信息
### Oracle Background Processes
- 初始时创建的用于维护内存结构、进行异步数据I/O读写及日常的维护任务，从而更好地实现增加并行操作的可靠性

## 数据库管理
### Control Files
    物理组成元件，可复制即实现复用
### Tablespaces
- 逻辑结构
    + 包含一个或多个Datafiles或Tempfiles.
- 默认表空间
    - EXAMPLE：Oracle测试、示例空间
    - SYSAUX：SYSTEM辅助空间
    - SYSTEM：含数据字典等控制数据库信息的表和索引
    - TEMP：执行SQL用作系统的临时操作空间
    - UNDOTBS1：专用存储撤消操作信息
    - USERS：存储永久用户对象和数据
- 表空间类型
    - 永久：用于存储用户或程序数据
    - 撤消：用于避免读一致和启用数据回滚，不用时会被自动删除，多个情况下同时也只- 一个会被启用
    - 临时：用于执行SQL排序等操作时的辅助库
### Temporary Tablespace Groups
### Datafiles
- 保存数据库数据的操作系统文件，但用数据库方式写入，无法为其它程序识别。
- 可分化组件：段和区（多块邻近数据块组成）、数据块（最小IO元件，建库后不可变更）
### Rollback Segments
### Redo Log Groups
- 多份重做日志，避免由于错误操作导致变更数据永久写入数据文件而无法恢复。
- 多重做日志组（通常有三组），通过内存缓冲区写入数据直到组信息满或请求切换，并循环切换。
### Archive Logs
- 重做日志的异地存储复本，可用于数据恢复。

## 数据库空间回收
- 收缩操作
    + 对表进行操作，不影响表的DML操作
- 重组操作
    + 重组表空间中的不同部分，但必须事先拥有与对象大小相等的自由空间才能成功
- 人工清表

## 还原数据
- 概念
    + 事务操作前事先保存下来的数据
- 特点
    + 便于事务的快速回滚& 保持读一致性& 使用闪回表保存

## ORACLE报告顺序
- <1>根据WHERE子句选择行
- <2>根据GROUP BY子句将这些行分组
- <3>为第一组计算分组函数的结果
- <4>根据HAVING子句选择和排除组
- <5>根据ORDER BY子句中分组函数的结果对组进行排序
    - ORDER BY子句必须使用分组函数（COUNT(*)）
    - 或者使用在GROUP BY子句中指定的列


## ORACLE常用函数
### CHAR
- TO_CHAR
    + TO_CHAR(SYSDATE, 'DDD')
    + TO_CHAR(TO_DATE('2002-08-26', 'YYYY-MM-DD'),'DAY','NLS_DATE_LANGUAGE = FRENCH')
    + TO_CHAR('11111111.', '$99999,999.99')
        * $11111,111.00
    + TO_CHAR(TO_DATE(222, 'J'), 'Jsp')
        * Two Hundred Twenty-Two
    + TO_CHAR(TO_DATE(222, 'J'), 'JSP')
        * TWO HUNDRED TWENTY-TWO

### TIME
- TO_DATE
    + TO_DATE('2013-02-18 22:20:15', 'YYYY-MM-DD HH24:MI:SS')
    + TO_DATE('2013', 'YYYY')
- BETWEEN
    + FLOOR(SYSDATE - TO_DATE('20130101', 'YYYYMMDD'))
    + FLOOR(MONTHS_BETWEEN(TO_DATE('20131231', 'YYYYMMDD'), TO_DATE('20010201', 'YYYYMMDD')))
- NEXT_DAY
    + NEXT_DAY(SYSDATE, 1)
    + ADD_MONTHS(SYSDATE, 2)
- NEW_TIME
    + NEW_TIME(SYSDATE, 'GMT', 'EST')
        * 大西洋标准时间：AST或ADT 
        * 阿拉斯加_夏威夷时间：HST或HDT 
        * 英国夏令时：BST或BDT 
        * 美国山区时间：MST或MDT 
        * 美国中央时区：CST或CDT 
        * 新大陆标准时间：NST 
        * 美国东部时间：EST或EDT 
        * 太平洋标准时间：PST或PDT 
        * 格林威治标准时间：GMT 
        * Yukou标准时间：YST或YDT

### FUNCTION
- ROUND
    + ROUND(SYSDATE [,'[DD|year|mouth|day]'')
        * 获取最近的 [日期|年|月|周日] 日期
    + TRUNC(SYSDATE [,'[DD|year|mouth|day]'')
        * 截取 [当天|当年首日|当月首日|周日] 的时间，且无时分秒
- BYTE
    + TO_MULTI_BYTE('高A')
        * 将字串的半角转换为全角
    + TO_SINGLE_BYTE('高Ａ')
        * 全角转半角
- NULL
    + NVL(EXP1, EXP2)
        * NULl == EXP1? EXP2: EXP1
        * 两者类型要求一致
    + NVL2(EXP1, EXP2, EXP3)
        * NULL== EXP1? EXP3: EXP2
        * EXP2与EXP3类型不同，则EXP3自动转换为EXP2的类型
    + NULLIF(EXP1, EXP2)
        * EXP1== EXP2? NULL: EXP1
    + COALESCE(EXP1, EXP2,...EXPN)
        * 返回列表中第一个非空表达式，若均空则返回空值
- TYPE
    + DUMP(INPUT[,TYPE [,Y [,Z]]]
        * 返回INPUT的数据类型，字节长度及内部存储位置
    + GREATEST(EXP1, EXP2,..., EXPN)
    + LEAST(EXP1, EXP2,..., EXPN)
    + LENGTH/LENGTHC(INPUT)
        * 一个汉字算一个字符
    + VSIZE/LENGTHB(INPUT)
        * 一个汉字算两个字节
