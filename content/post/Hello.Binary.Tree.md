---
title: "Hello.Binary.Tree"
date: "2017-08-12"
categories:
 - "整理"
tags:
 - "Algorithm"
toc: true
---


# 类型
## 二叉树
- 定义
    - 每个结点最多有两个子树的树结构
- 图示
    - ![二叉树.jpg](http://otzm88f21.bkt.clouddn.com/e8bc9be9-b0ac-4ba7-a220-fbdea61c8bba.jpg)
- 遍历
    - DLR
    - LDR
    - LRD
- 存储
    - 顺序存储
        - left:   2* n+ 1
        - right:  2* n+ 2
        - parent: (n-1)/ 2
    - 链表存储


## 二叉排序树
- 性质
    - 左子树所有结点均小于其根结点的值
    - 右子树所有结点均大于其根结点的值
    - 左右子树亦为二叉排序树
    - 无键值相等的节点
- 图示
    - ![二叉排序树.jpg](http://otzm88f21.bkt.clouddn.com/d04afa12-458a-46cd-9a10-3cd8bfa1af5a.jpg)


## AVL树
- 定义
    - 自平衡二叉排序树
- 性质
    - 每个结点的左右子树调度之差的绝对值最多为1
- 图示
    - ![AVL树.png](http://otzm88f21.bkt.clouddn.com/350a69d0-c61b-45dd-9057-618576a785b9.png)
    - ![AVL树-Order.png](http://otzm88f21.bkt.clouddn.com/d5554d49-ca3e-4bf3-82f3-e3402c3e2916.png)


## 红黑树
- 定义
    - 自平衡二叉排序树
- 性质
    - 节点是红色或黑色
    - 根节点是黑色
    - 叶节点是黑色
    - 红色节点的子节点均为黑色
    - 从任一节点到其各叶子节点的所有路径，都包含相同数目的黑色节点
- 注意
    - 插入元素均为红色，然后再通过变色、旋转进行调整
- 图示
    - 详见整理


# 参考
## 参考
- [二叉树](https://baike.baidu.com/item/%E4%BA%8C%E5%8F%89%E6%A0%91)
- [二叉排序树](https://baike.baidu.com/item/%E4%BA%8C%E5%8F%89%E6%8E%92%E5%BA%8F%E6%A0%91gg)
- [AVL树](https://baike.baidu.com/item/AVL%E6%A0%91)
- [红黑树](https://zh.wikipedia.org/wiki/%E7%BA%A2%E9%BB%91%E6%A0%91)
- [红黑树](https://baike.baidu.com/item/红黑树)

## 整理
- [红黑树.xmind](http://otzm88f21.bkt.clouddn.com/bdbf2158-8a72-4de1-9f37-60ad9e7194eb.xmind)