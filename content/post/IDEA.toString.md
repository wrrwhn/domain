---
title: "IDEA.toString 生成覆盖"
date: "2019-06-05"
categories:
 - "整理"
tags:
 - "IDEA"
toc: true
---


# 前言
- 业务中常用 `Jackson` 等工具，进行对象的 `Json` 格式展示，友好但可能不够高效
- 而常用的 `toString()` 方法覆盖中使用 `=` 连接，没有标准 `Json` 通用
- 故考虑通过添加模板来快速生成类的 `Json` 格式的 `toString()` 覆盖

# 流程
## 使用模板生成 `toString` 
- 键入 `Alt+ Insert` 并选择 `toString()` 的生成器
- 在生成器中选择模板右侧的 `Settings` 按钮添加模板
    - ![idea.toString.generator.png](http://doc.yqjdcyy.com/608d057a-3d84-417b-b2b8-e2774a11f09e.png)

## 添加新模板 `Json`
- 切换至 `Templates` 标签后进行添加
    - ![idea.toString.template.png](http://doc.yqjdcyy.com/529dc539-6eb6-4da5-9ac5-e50174b8d98d.png)

## 添加模板内容
- 注意内容中的空格会体现在最后的生成样式中
```groovy
public java.lang.String toString() {
final java.lang.StringBuilder builder = new java.lang.StringBuilder("{");
#set ($i = 0)
#foreach ($member in $members)
#if ($i == 0) builder.append(" #else builder.append(", #end
#if ($member.string || $member.date)\"$member.name\":\"") #else\"$member.name\":") #end
#if ($member.primitiveArray || $member.objectArray) .append(java.util.Arrays.toString($member.name));
#elseif ($member.string || $member.date) .append($member.accessor).append('\"'); #else.append($member.accessor);#end
#set ($i = $i + 1)
#end
builder.append('}');
return builder.toString();
}
```

## 校验
- 使用刚选择的 `Json` 模板进行 ToString 生成

```java
    @Override
    public String toString() {
        final StringBuilder builder = new StringBuilder("{");
        builder.append(" \"id\":").append(id);
        builder.append(", \"version\":").append(version);
        builder.append('}');
        return builder.toString();
    }
```


# 参考
- [IDEA修改toString方法模板为JSON格式](https://blog.csdn.net/MasonQAQ/article/details/77975030)