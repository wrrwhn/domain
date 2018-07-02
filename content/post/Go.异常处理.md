---
title: "Go.异常处理"
date: "2017-05-23"
categories:
 - "整理"
tags:
 - "Go"
toc: true
---



## Defer, Panic, Recover

### 参考
- [Defer, Panic, and Recover](https://blog.golang.org/defer-panic-and-recover)
- [Go的异常处理 defer, panic, recover](http://www.cnblogs.com/ghj1976/archive/2013/02/11/2910114.html)

### 使用
#### `defer`
- 作用
    + 用于在当前方法结束时，执行相关方法
- 特点
    + 声明 `defer` 时值即为方法中参数被赋的值
    + 声明多个时，即以`后进先出`的顺序执行
    + 可读取、操作命名的方法返回参数对象
        * 操作返回值
        ```
        // return 2
        func c() (i int) {
            defer func() { i++ }()
            return 1
        }
        ```
#### `panic`
- 特点
    + 内置方法
    + 立即中断当前程序
    + 方法内原`defer`定义方法仍可正常进行
    + 向上抛出该程序崩溃处指针

#### `recover`
- 特点
    + 内置方法
    + 仅可用于`defer`方法中
    + 用于捕获`panic`抛出的异常信息

### 实例
```
func main(){
    // 必须要先声明defer，否则不能捕获到panic异常
    defer func(){
        fmt.Println("c")
        if err:=recover();err!=nil{
            // 这里的err其实就是panic传入的内容，55
            fmt.Println(err)
        }
        fmt.Println("d")
    }()
    f()
}

func f(){
    fmt.Println("a")
    panic(55)
    fmt.Println("b")
    fmt.Println("f")
}

// 输出信息为：a/ c/ 55/ d
```
