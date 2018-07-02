---
title: "Oracle.分析函数"
date: "2016-12-15"
categories:
 - "整理"
tags:
 - "Oracle"
toc: true
---


## 统计方面
### 合并
- Sum( ) Over ([Partition by  ] [Order by  ])
- ROLLUP(COL1, COL2...)
    + 按照COLX的降序层次逐层汇总合并
- CUBE(COL1, COL2...)

### 排序
- Rank() Over ([Partition by  ] [Order by  ] [Nulls First/Last])
    + 相同数据排名一致，但下一记录会空出其中的排名
- Dense_rank() Over ([Patition by  ] [Order by  ] [Nulls First/Last])
    + 相同数据排名一致，之后数据接上一次序继续
- Row_number() Over ([Partitionby  ] [Order by  ] [Nulls First/Last])
    + 碰到相同数据时不做处理依照记录顺序依次递增
- Ntile( ) Over ([Partition by  ] [Order by  ])

### 最值
- [Min|Max]() Keep (Dense_rank First/Last [Partition by  ] [Order by  ])
    + MIN           用于确保返回唯一记录
    + KEEP          用于告知Oracle保留符合KEEP条件的记录
    + Dense_rank    固定写法，不可更改
- Ratio_to_report(value)
    + Ratio_to_report(value)= value/ sum(value)

### 首/末记录
- [First_value|Last_value|(Sum() Over ([Patition by  ] [Order by  ] Rows Between   Preceding And   Following  ))

### 相邻记录比较
- LAG(其它行表达式, <偏移行数，缺省为1>, <偏移行数超出分组范围时的返回值>)
    + LAG(SUM( ), 1) OVER([PATITION BY] [ORDER BY ])
- LEAD(其它行表达式, <偏移行数，缺省为1>, <偏移行数超出分组范围时的返回值>)
    + LEAD(SUM( ), 1) OVER([PATITION BY] [ORDER BY ])

### 家族树
- START WITH COL1= 'XXX' CONNECT BY COL1= PRIOR COL2
    + 语法
        + 1>语句顺序：SELECT> FROM> WHERE> START WITH> CONNECT BY> ORDER BY
        + 2>PRIOR位置：在前（CONNECT BY PRIOR COL1=COL2），强制由根到叶自顶向上，置于后面则相反由叶到根
        + 3>WHERE可以排除树中个体，但无法排除其子孙或祖先
        + 4>CONNECT BY条件则可以排除个体及其后代信息
        + 5>CONNECT BY不以和WHERE子句中表连接使用
    + 例子
    ```           
    SELECT LEVEL,
           NVL(B.COW, 'EVES-COW') COW, NVL(B.BULL, 'EVE-BULL') BULL,
           NVL(LPAD(B.OFFSPRING, 6*(LEVEL- 1), ' '), 'EVE')AS OFFSPRING,   --LEVEL为相当于树层级，为自动带出
           B.SEX, B.BIRTHDATE
    FROM BREEDING B
    START WITH B.OFFSPRING= 'EVE'           --起始节点
    CONNECT BY
            COW= PRIOR B.OFFSPRING          --相当于自表关联的条件
            AND B.OFFSPRING!= 'MANDY';      --有效地去除该分支，而不像WHERE只能去除该指定对象
    ```
