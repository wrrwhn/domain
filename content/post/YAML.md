---
title: "Hello.YAML"
date: "2018-08-08"
categories:
 - "整理"
tags:
 - "YAML"
toc: true
---

# YAML

## 描述
- 专业配置文件语言

## 规则
- 大小写敏感
- 使用缩进表示层级关系
    - **不允许 Tab**，只允许空格
    - 缩进空格数无关，同层左侧对齐即可
    - 冒号后面须跟空格
- 支持类型
    - 对象
    - 数组
    - 纯量


## 示例

```yaml
# 对象
article: 
    title: 'title'
    content: 'content'

article: {title: title, content: content}

# 数组
array:
    - 'item-1'
    - 'item-2'
    - 'item-3'

array: ['item-1', 'item-2']

# 纯量
## 文本行
str: 
    - string            # 字符串默认不加引号
    - 'string''string'  # 文本中引入单引号
    - "string"
## 文本块
# xxx\nyyy\n 其中(+|-) 用于指定是否保留文本块末尾的换行
str: |[+|-]
     xxx
     yyy
# xxx yyy\n
str: >
     xxx
     yyy

bool: true
int: 1
float: 1.1
null: ~
time: 2018-08-08t21:59:43.10-05:00 
date: 2018-08-08

# 强制类型转换
str: !!str 123  

# 引用
## `&v0` 用于创建 v0 锚点，而 `*vo` 用于引用 v0 锚点  
v0: &v0
    detail: v0.detail
    log: v0.change.logs
v1:
    detail: v1.detail
    log: change.logs
    history: *v0
### history:
###    detail: v0.detail
###    log: v0.change.logs
```


# 参考
- [YAML 简介](https://www.ibm.com/developerworks/cn/xml/x-cn-yamlintro/index.html)
- [YAML 语言教程](http://www.ruanyifeng.com/blog/2016/07/yaml.html)
- [%YAML 1.2](http://yaml.org/)
- [YAML Ain’t Markup Language (YAML™) Version 1.2](http://yaml.org/spec/1.2/spec.html)
- [go-yaml/yaml](https://github.com/go-yaml/yaml)
- [golang yaml配置文件解析](https://www.cnblogs.com/hanyouchun/p/6724056.html)