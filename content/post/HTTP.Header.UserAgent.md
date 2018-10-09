---
title: "HTTP.Header.UserAgent"
date: "2018-10-09"
categories:
 - "整理"
tags:
 - "HTTP"
toc: true
---


# Introduce
## 作用
- User-Agent 请求头包含一特征文本，供允许网络协议获取应用类型、操作系统、软件供应商和软件版本
    - 统计用户浏览器的使用情况
- 请求服务提供商为当前 PC/ WEb 提供的特定格式网页
    - 前端实现上需要考虑浏览器兼容问题

# 语法
## 格式
- `User-Agent: <product> / <product-version> <comment>`
- `User-Agent: Mozilla/<version> (<system-information>) <platform> (<platform-details>) <extensions>`
    - `User-Agent: Mozilla/5.0 (platform; rv:geckoversion) Gecko/geckotrail Firefox/firefoxversion`
- `User-Agent: <浏览器标识> (<操作系统标识>; <加密等级标识>; <浏览器语言>) <渲染引擎标识> <版本信息>`
## 选项
- `<product>`
    - 产品识别码| 浏览器标识
    - `Mozilla/5.0`
        - 通用标识符，表示与 `Mozilla` 兼容
        - 早期服务器于加载前检查是否为 `Netscape` 浏览器，其它浏览器不得不伪装

- `<product-version>`
    - 产品版本号| 渲染引擎标识

- `<comment>`
    - 产品相关备注信息 |版本信息
    
- `rv:geckoversion`    
    - `Gecko` 的发布版本号
    - `geckoversion` 与 `firefoxversion` 相同

- `system-information`
    - 操作系统标识
    - 如若有多个，则以 `;` 进行分隔

        | 操作系统 |             版本标识             |
        |----------|----------------------------------|
        | FreeBSD  |                                  |
        |          | X11; FreeBSD (version no.) i386  |
        |          | X11; FreeBSD (version no.) AMD64 |
        | Linux    |                                  |
        |          | X11; Linux ppc                   |
        |          | X11; Linux ppc64                 |
        |          | X11; Linux i686                  |
        |          | X11; Linux x86_64                |
        | Mac      |                                  |
        |          | Macintosh; PPC Mac OS X          |
        |          | Macintosh; Intel Mac OS X        |
        |          | Solaris                          |
        |          | X11; SunOS i86pc                 |
        |          | X11; SunOS sun4u                 |
        | Windows  |                                  |
        |          | Windows NT 6.1 (windows 7)       |
        |          | Windows NT 6.0 (windows vista)   |
        |          | Windows NT 5.2 (windows 2003)    |
        |          | Windows NT 5.1 (windows xp)      |
        |          | Windows NT 5.0 (windows 2000)    |
        |          | Windows ME                       |
        |          | Windows 98                       |
        | Android  |                                  |
        | Iphone   |                                  |
        | Mobile   |                                  |


- `Gecko/geckotrail`
    - 说明使用浏览器基于 `Gecko` 渲染引擎
    - 桌面浏览器，`geckotrail` 固定为 `20100101`

- `Firefox/firefoxversion`
    - 说明该浏览器是 `Firefox`

- 加密等级标识

    | 标识 | 含义                        |
    |------|---------------------------|
    | N    | 无安全加密                  |
    | I    | 弱安全加密<br>**40位** 加密  |
    | U    | 强安全加密<br>**120位** 加密 |

- 浏览器语言
    - 表示当前浏览器指定的语言

- 渲染引擎标识

## 示例
- Firefox 
    - `Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0`
    - `Mozilla/5.0 (Macintosh; Intel Mac OS X x.y; rv:42.0) Gecko/20100101 Firefox/42.0`
- Chrome 
    - `Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36`
- Opera 
    - `Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.106 Safari/537.36 OPR/38.0.2220.41`
- Safari 
    - `Mozilla/5.0 (iPhone; CPU iPhone OS 10_3_1 like Mac OS X) AppleWebKit/603.1.30 (KHTML, like Gecko) Version/10.0 Mobile/14E304 Safari/602.1`
- Internet Explorer
    - `Mozilla/5.0 (compatible; MSIE 9.0; Windows Phone OS 7.5; Trident/5.0; IEMobile/9.0)`
- 爬虫和机器人
    - `Googlebot/2.1 (+http://www.google.com/bot.html)`


# Supplement

## 浏览器发展史
- NCSA.Mosaic
- Netscape.Mozilla
- Microsoft.IE
- Firefox.Gecko
- KHTML
    - Konqueror
        - WebKit
            - Mac.Safari
        - Google.Chrome
- Opera        

## 浏览器

| 浏览器名称        | 包含关键词                   | 禁止包含关键词               | 补充                                                                                     |
|-------------------|------------------------------|------------------------------|------------------------------------------------------------------------------------------|
| Firefox           | Firefox/xyz                  | Seamonkey/xyz                |                                                                                          |
| Seamonkey         | Seamonkey/xyz                |                              |                                                                                          |
| Chrome            | Chrome/xyz                   | Chromium/xyz                 |                                                                                          |
| Chromium          | Chromium/xyz                 |                              |                                                                                          |
| Safari            | Safari/xyz                   | Chrome/xyz <br> Chromium/xyz | Safari 有两个版本号，一个技术性较强，格式是Safari/xyz，一个对用户友好一点，格式是Version/xyz |
| Opera             | OPR/xyz [1]<br>Opera/xyz [2] |                              | [1] Opera 15+ (基于Blink的引擎) <br>[2] Opera 12- (基于Presto的引擎)                     |
| Internet Explorer | ; MSIE xyz;                  |                              | IE浏览器的名字并没有使用BrowserName/VersionNumber的格式                                  |

## 渲染引擎

| 渲染引擎名称 |   包含关键字    |                                                 补充                                                |
|--------------|-----------------|-----------------------------------------------------------------------------------------------------|
| Gecko        | Gecko/xyz       |                                                                                                     |
| WebKit       | AppleWebKit/xyz | 请注意，WebKit浏览器包含了'like Gecko'字符串，如果不加以注意，可能会触发检测Gecko的false positive。 |
| Presto       | Opera/xyz       | 注: 在Opera浏览器在15及以上的版本中，已经不再使用Presto（参见'Blink'）。                            |
| Trident      | Trident/xyz     | IE浏览器将此字符串放在User Agent字符串的注释部分。                                                  |
| Blink        | Chrome/xyz      |                                                                                                     |


# Reference
## 官方
- [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)
    - [User-Agent 中文版本](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/User-Agent)
- [Browser detection using the user agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Browser_detection_using_the_user_agent)
    - [Browser detection using the user agent 中文版本](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Browser_detection_using_the_user_agent)
- [Firefox user agent string reference](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent/Firefox)

## 补充
- [认识User-Agent](https://blog.csdn.net/rj042/article/details/6991441)
- [Browser Statistics](https://www.w3schools.com/browsers/default.asp)