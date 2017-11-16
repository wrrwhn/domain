+++
date = "2017-04-24T22:07:46+08:00"
title = "Go.Flag"
draft = false
tags = ["整理","Go"]
share = true
+++

[TOC]

## Flag

### 参考
- [Package flag](https://golang.org/pkg/flag/ )
- [Golang flag包使用详解](http://faberliu.github.io/2014/11/12/Golang-flag%E5%8C%85%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3-%E4%B8%80/ )


### 作用
- 用于解析命令行参数的功能接口

### 常用
- `flag.Xxx[String| Bool| Int](name string, default-value Xxx, usage-info string)`
    + 返回相应__指针__
- `flag.XxxVar(ptr-store *Xxx, name, default-value, usage-info)`
    + 将 flag __绑定到变量__
- `flag.Var(&custom-type, name, usage-info)`
    + custom-type 需要实现 Value 接口
- `flag.Parse()`
    + 直接使用国指针，绑定参数则为实际值
    + flag.Args()
    + flag.Arg(i)
- `flag.PrintDefaults`
    + define
        * `flag.String("I", "", "search `directory` for include files")`
    + output
        ```
        -I directory
            search directory for include files.
        ```

### 示例
- 引用后获取相关信息
    ```
    url := flag.String("url", "127.0.0.1", "input the url, plz")
    port := flag.Int("port", 8888, "input the port, plz")
    flag.Parse()

    fmt.Printf("%v %v\n", *url, *port)
    ```
- 获取“species” flag的值，默认为“gopher”
    + `var species = flag.String("species", "gopher", "the species we are studying")`
-  两个flag共享同一个变量，一般用于同时实现完整flag参数和对应简化版flag参数，需要注意初始化顺序和默认值
    ```
    var gopherType string

    func init() {
      const (
        defaultGopher = "pocket"
        usage         = "the variety of gopher"
      )
      flag.StringVar(&gopherType, "gopher_type", defaultGopher, usage)
      flag.StringVar(&gopherType, "g", defaultGopher, usage+"(shorthand)")
    }
    ```
