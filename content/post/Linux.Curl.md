+++
date = "2017-04-01T22:07:46+08:00"
title = "Linux.Curl"
draft = false
tags = ["整理","Linux"]
share = true
+++


[TOC]

## 参考
- [curl网站开发指南](http://www.ruanyifeng.com/blog/2011/09/curl.html)
- [curl 官网](https://curl.haxx.se/)
- [everything-curl.pdf](https://www.gitbook.com/download/pdf/book/bagder/everything-curl)

## 指令
- `-O` | `-o path`
    + 下载
- `-i`
    + 显示 http response 头信息
- `-v`
    + 显示通信全过程信息
    + 可使用 `--trace output.txt` 保存更详细通信过程
- `-X [DELETE| POST| PUT| ...]`
    + 支持其它类型参数
- `--data "arg=val"`
    + 传递参数
- `--user-agent`
    + 传递客户端设备信息
- `-c cookie-file + -b cookie-file`
    + 分别用于保留服务器返回的 cookie 至指定文件，和请求时读取文件作为 cookie 信息
- `-header "Content-Type:application/json"`
    + 增加头信息

## 示例
- 带 cookie 请求
    + `curl -X POST -H "Content-Type: application/json;charset=UTF-8" -d '{"username":"15880777595","pwd":"1"}' --cookie-jar /data/cdn/resource/curl-cookie http://live.yunkai.com/basic-api/login`
    + `curl -X POST --cookie /data/cdn/resource/curl-cookie http://test-live.yunkai.com/api/reset/topic/1101?file=2db80451-154d-482c-8a65-07ba5168a904.pptx `
    + `curl -v --cookie /data/cdn/resource/curl-cookie http://live.yunkai.com/basic-api/exercises/142 > /data/tmp/yao/142`
- 表单提交
    + `curl --form arg1=val1 --form arg2=val2 {URL} `
- Multifile Request
    + `curl -F file=@"/data/cdn/test/doc/2e8e1880-ba8c-4166-b4b1-c40205af3c95.txt" http://test-live.yunkai.com/api/resource/uploadError?type=back`
- 下载
     + `curl -v -o courseware-106.zip --cookie /data/tmp/yao/curl/curl-cookie "http://live.capitalnuts.com/basic-api/courseware/106/talk"`
