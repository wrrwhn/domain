---
title: "Python.__main__"
date: "2018-03-26"
categories:
 - "整理"
tags:
 - "Python"
toc: true
---


# 参考
- [Python 中 __name__ == '__main__' 的作用](https://blog.csdn.net/liang19890820/article/details/75081689)
- [if __name__ == '__main__' 如何正确理解?](https://www.zhihu.com/question/49136398)

# 作用
- 保障模块既可以被导入，也可作为脚本来执行
	- 直接运行时，模块名为 **__main__**，因为对应代码块可被运行
	- 导入时，代码块不执行