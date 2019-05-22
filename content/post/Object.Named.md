---
title: "Java.Object.Named"
date: "2019-05-22"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---

# 各层级对象命名后缀

| 层级   | 对象   | 描述                                                  |
|--------|--------|-----------------------------------------------------|
| 数据库 | PO     | Persistant Object <br>表对象                          |
|        | DAO    | Data Acess Object  <br> 持久化对象，包含数据库操作     |
|        | Entity | 参见 PO                                               |
|        | DO     | Domain Object<br>抽象的业务领域集合                   |
| 业务层 | BO     | Business Object<br>业务模型对象                       |
|        | DTO    | Data Transfer Object<br>不同数据层的传输对象          |
| 视图层 | VO     | View Object<br>展示层对象                             |
| 其它   | POJO   | Plian Ordinary Java Object<br>仅具备GET/SET的JavaBean |