---
title: "Mysql.开发规范"
date: "2016-12-11"
categories:
 - "整理"
tags:
 - "Mysql"
toc: true
---


# [数据库开发规范简版]
## 数据库创建
### 命名规范
- Pascal样式(每个单词首字母大写)命名
- 命名格式为[项目英文名称]
### 例
- CREATE DATABASE `test` CHARACTER SET 'utf8' COLLATE 'utf8_general_ci';

## 表创建
### 命名规范
#### 表
- 以组件或子系统名称为前缀
- 添加表和字段注释，明确各值可选值含义
#### 列
- 列名称命名采用英文单词或缩写，关联具体业务
- 尽量不允许为 Null，用默认值代替
#### 例
- FetionUser.FU_TableName.ColName

### 类型选择
#### 整型
- tinyint
- 2^8
- -128到127或0到255
- smallint
- 2^16
- -32768到32767或0到65535
- int
- 2^32
- -2147483648到2147483647或0到4294967295
- bigint
- 2^64
- -9223372036854775808到9223372036854775807或0到18446744073709551615
- 例
- tinyint(3) 明确指定宽度
- 确认无符号时，需要添加 unsigned 限制

#### 文本
- CHAR(1)
- VARCHAR(2- 20000)
- TEXT
- 单表维护！

#### 时间
- timestamp
- datetime


## 索引创建
### 命名规范
- IX_[TableName]_[Column1]_[Column2]
- 字段与索引顺序一致

### 主键设计
- InnoDB 必须有自增主键，建议为 int 且与业务无关
- 唯一索引

### 复合索引
- MYSQL 搜索顺序同索引字段顺序

### 建议
- 索引个数控制于 3 个以内，不超过 5 个


## SQL 编程
### SELECT
- SELECT COLUMN_NAME, NOT SELECT *

### 多表关联
- 为各表使用别名

### DISTINCT
- 唯一索引不需要
- 可考虑程序去重

### OR
- 多个 OR 或 AND 常会导致表扫描
- 可用 UNION代替 OR，或 FORCE INDEX 强制使用主索引

### COUNT
- Use COUNT(*) in one table and no WHERE

### LIMIT
- 建议
- 尽量使用 LIMIT M，避免 LIMIT M,N
- 通过条件和 LIMIT M 来代替 LIMIT M,N
- 例
- SELECT * FROM message WHERE id > 9520 ORDER BY id ASC LIMIT 20;

### !=
- 避免 != | <> ，使用 > | < | >= | <= 代替，从而正常使用索引

### 避免 IN
- 建议
- IN 只接常量
- 子查询使用表关联代替
- 例
- select * from tb1 where tb1.id in (select id from tb2 where tb2.c1…)
- Select tb1.* from tb1 , (select id from tb2 where tb2.c1…)t where tb1.id = t.id
