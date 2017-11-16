+++
date = "2016-12-11T22:07:46+08:00"
title = "Markdown"
draft = false
tags = ["整理","Markdown"]
share = true
+++

# **目录**
[TOC]

# 标题
## 二级标题
####### 最大支持六级标题

-----
**注：序号和*号，与内容之间均需要一个空格**

1. 列表1
2. 列表2
3. 列表3

* 无序列表
* 无序列表
    * 无序列表缩进
- 无序列表

-----

字体支持很多样式，比如**加粗**、*斜体*、~~删除线~~等特别样式

-----

当然也可以引用名人名言
>Markdown 很好用的 —— 阿尔伯特·爱因斯坦

-----

[百度](www.baidu.com)

-----

代码格式表示
```
    for(var i= 0; i< count; i++)
```

-----

表格
|为知| md|
|-|-|
|WizNote|Markdown|


-----

公式啊！！！
$\Gamma(n) = (n-1)!\quad\forall n\in\mathbb N$

-----

```flow
st=>start: Start:>http://www.google.com[blank]
e=>end:>http://www.google.com
op1=>operation: My Operation
sub1=>subroutine: My Subroutine
cond=>condition: Yes
or No?:>http://www.google.com
io=>inputoutput: catch something...
st->op1->cond
cond(yes)->io->e
cond(no)->sub1(right)->op1
```
----

```sequence
Title:  [教程可以看](http://bramp.github.io/js-sequence-diagrams/)
A->B: 【备注演示】
Note left of A: 此轮回应前\nA 左侧备注
Note over A: 此轮 A 线上的备注
Note right of A: 此轮回应前A 右侧备注
Note over A,B:此轮回应前横跨 AB 两线的备注
A->B: 【线型演示】
A->B: 实线三角标
B-->A: 虚线三角标
A->>B: 实线角标
B-->>A: 虚线角标
```

-----
