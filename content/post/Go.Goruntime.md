+++
date = "2017-09-13T22:07:46+08:00"
title = "Go.并行&并发 "
draft = false
tags = ["整理", "Go", "GoRuntime "]
share = true
+++

## 参考
- [Go语言并发与并行学习笔记](http://www.golang.ltd/forum.php?mod=viewthread&tid=6159)
- [What exactly does runtime.Gosched do?](https://stackoverflow.com/questions/13107958/what-exactly-does-runtime-gosched-do)
- [关于GoRoutine的一个运行问题](https://segmentfault.com/q/1010000000207474)

## 概念
### 默认设置
- 所有 goruutime 均在单原生线程中运行，仅占用单 CPU
- 当前 goruutime 不阻塞则不会让出 CPU 时间给其它同线程的 goruutime
- 当 goruutime 阻塞时， Go 将自动将此线程的其它 goruutime 转换到其它运行中的系统线程

### 并行& 并发
- ![](http://hit9.qiniudn.com/con_and_par.jpg)
- 并行
    - 多套环境
    - 配套任务队列和消息处理
- 并发
    - 一套环境
    - 多任务队列和单一消息处理


## 重点
- 显式多核
    - `runtime.GOMAXPROCS`
- 手动显式调用
    - `runtime.Gosched`

## 示例
- 单线程多任务串行
    - 并发，逐一执行
    ```
    var quit chan int = make(chan int)

    func loop() {
        for i := 0; i < 10; i++ {
            fmt.Printf("%d ", i)
        }
        quit <- 0
    }


    func main() {
        go loop() * 2

        for i := 0; i < 2; i++ {
            <- quit
        }
    }
    ```

- 单线程多任务并发
    - 并发，多任务阻塞后切换执行
    ```
    var quit chan int

    func foo(id int) {
        fmt.Println(id)
        time.Sleep(time.Second)
        quit <- 0
    }


    func main() {
        count := 1000
        quit = make(chan int, count)

        for i := 0; i < count; i++ {
            go foo(i)
        }

        for i :=0 ; i < count; i++ {
            <- quit
        }
    }
    ```

- 多线程并行
    - 两个线程任务同时进行
    ```
    var quit chan int = make(chan int)

    func loop() {
        for i := 0; i < 100; i++ {
            fmt.Printf("%d ", i)
        }
        quit <- 0
    }

    func main() {
        runtime.GOMAXPROCS(2)
        go loop()* 2
        for i := 0; i < 2; i++ {
            <- quit
        }
    }
    ```

- 单线程并发
    - 多线程多任务显式切换
    ```
    func loop() {
        for i := 0; i < 10; i++ {
            runtime.Gosched()
            fmt.Printf("%d ", i)
        }
        quit <- 0
    }
    ```
