+++
date = "2018-05-10T00:00:00+08:00"
title = "Hello.性能测试"
draft = false
tags = ["阅读","测试","性能"]
share = true
+++

[TOC]

# Mistake
- 用的全是 **平均** 值
- **响应时间** 没有和**吞吐量 **TPS/QPS** 挂钩
- **响应时间和吞吐量** 没有和 **成功率** 挂钩

# 调整
## Averages not work
### 噪点影响平均值
- 统计数据
    - [1ms,1ms,1ms,1ms,1ms,1ms,1ms,1ms,1ms,`1s`]
- 平均值
    - 100ms
- 实际作法
    - 去除噪点后计算平均值
    - 或将数据排序后，取中间数据的平均值

### 百分比分页
- 统计数据
    - [10ms, 1s, 200ms, 100ms]
- 报告
    - TP= Top Percentile = 当前数值的最高可能性
    - TP50<= 100ms
    - TP90<= 1s
### 推荐格式
- `99.9%`的请求必须小于 `1ms`，所有的`平均时间`必须小于`1ms`

## Latency & Thoughput
### 关联
- 随着并发量（吞吐量）的上升，系统愈发不稳定，响应时间延长
- ![BenchmarkOptimalRate.png](http://otzm88f21.bkt.clouddn.com/58bda10e-0315-47be-859b-b02a6c39b87d.png)

### 推荐格式
- `TP99`小于`100ms`的时候，系统可以承载的最大并发数是`1000qps`

# #Thoughput & Success.Rate
- 性能测试，建立于请求的成功

# Recomend
## Point
- 吞吐量
    - Thoughput
- 响应时间
    - Latency
- 资源利用
    - CPU/MEM/IO/Bandwidth
- 成功率
- 系统稳定性

## Report
- **定义**一个系统的响应时间 **latency**
    - 建议是TP99，以及成功率
- 在这个响应时间的限制下，找到 **最高** 的 **吞吐量**
- 在这个吞吐量做 **Soak Test**
    - 使用第二步测试得到的吞吐量连续7天的不间断的压测系统
- 找到系统的 **极限值**
    - 在成功率100%的情况下（不考虑响应时间的长短），系统能坚持10分钟的吞吐量。
- 做 **Burst Test**
    - 用第二步得到的吞吐量执行5分钟
    - 然后在第四步得到的极限值执行1分钟
    - 再回到第二步的吞吐量执行5钟
    - 再到第四步的权限值执行1分钟
    - 如此往复个一段时间，比如2天。
- **低吞吐量** 和 **网络小包** 的测试

# Reference
- [性能测试应该怎么做？](https://coolshell.cn/articles/17381.html)
- [Why Averages Suck and Percentiles are Great](https://www.dynatrace.com/news/blog/why-averages-suck-and-percentiles-are-great/)
