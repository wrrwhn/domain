+++
date = "2017-07-20T22:07:46+08:00"
title = "Go.Reflect"
draft = false
tags = ["整理","Go"]
share = true
+++


[TOC]

# Go.Reflect

## 参考
- [Package reflect](https://golang.org/pkg/reflect/)
- [](http://www.golang.ltd/forum.php?mod=viewthread&tid=6017)

## 类型和接口
### Go 为静态类型语言，变量有且只有一个静态类型，于编译时已确认
```
var r io.Reader
r = os.Stdin
r = bufio.NewReader(r)
r = new(bytes.Buffer)
// r 不管指向什么，类型永远是 io.Reader
```

### interface{}
- 任何具体值均有 0+个方法，因此 interface{} 变量能存储任何值
- Interface 变量存储值，赋给该变量的值 & 值类型的描述符

### 定律
- 反射可以将“接口类型变量”转换为“反射类型对象”
- 反射可以将“反射类型对象”转换为“接口类型变量”
- 如果要修改“反射类型对象”，其值必须是“可写的”（settable）

### 方法
- reflect.Type
    - Kind()
    - NumMethod() | Method(int)
    - NumField() | Field(i int) |
    - Elem()
        - 返回类型的元素类型
        - 非[Array| Chan| Map| Ptr| Slice]时 panic
- reflect.Value
    - `ValueOf(i interface{})`
    - `CanSet()`
    - `Elem()`
    - NumField() | Field(idx) | FieldByName(name)
    - Kind()
    - NumMethod() | Method(idx) | MethodByName(name)
    - SetFloat(val) | SetInt(val) | SetString(val)
