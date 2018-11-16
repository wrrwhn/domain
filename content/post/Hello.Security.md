---
title: "Hello.Security"
date: "2018-11-16"
categories:
 - "整理"
tags:
 - "Security"
toc: true
---


# 常见类型
## HTTP

### 适用场景
- 便捷性
- 支持跨浏览器

### 流程
- ![HTTP.png](http://doc.yqjdcyy.com/97a23896-4dc0-41fd-aecb-862e95771877.png)

### 注意事项
- 账号密码暴露，建议使用 HTTPS

## Session

### 适用场景
- 浏览器自动支持，实现简便

### 流程
- ![Session.png](http://doc.yqjdcyy.com/c8195bab-bf09-402d-95fd-7d45ce141e7c.png)


### 注意事项
- 会话劫持攻击

## Token

### 适用场景
- 单页面应用
- 微服务

### 流程
- ![Token.png](http://doc.yqjdcyy.com/1cb6c437-0535-4402-b374-49363df56639.png)

### 注意事项
- token 认证为无状态
    - 区别于 session，于服务器内存或 redis 中存在着值以标识该用户的状态
- JWT 为其中基于 Web 标准的一种 Token 认证实现

## OAuth

### 版本

| 版本号    | 特点                                               |
|-----------|--------------------------------------------------|
| OAuth1.0  | 因 **固定 Session** 影响其安全性                   |
| OAuth1.0a | 需使用用于数字签名的加密库<br>适用于**高风险**业务 |
| OAuth2.0  | 依靠 **HTTPS** 进行安全保障                        |

### 模式

| 模式                                        | 适用场景                                     | 授权类型            |
|-------------------------------------------|------------------------------------------|---------------------|
| 授权码<br>authorization code                | 通用第三方授权场景                           | response_type=code  |
| 简化<br>implicit                            | 移动**客户端**<br>网页客户端                 | response_type=token |
| 密码<br>resource owner password credentials | 客户端受用户信任场景<br>服务商或桌面操作系统 |                     |
| 客户端<br>client credentials                | 后端 API 相关操作                            |                     |


### 流程
#### 授权码
- ![oAuth-AuthrizationCode.png](http://doc.yqjdcyy.com/5985b50b-7efc-4124-8b3a-9183573ebf51.png)

#### 简化
- ![oAuth-Implicit.png](http://doc.yqjdcyy.com/6e71d8e2-d055-4451-aa39-18dc3bada78a.png)

#### 密码
- ![oAuth-PWD.png](http://doc.yqjdcyy.com/0153e024-bdb0-4faa-a82a-6207e194d945.png)

#### 客户端
- ![oAuth-Client.png](http://doc.yqjdcyy.com/450fb770-983a-48c6-9c02-5648b78f1bf5.png)

### 注意事项
- 场景中需要的 ClientID 于第三方服务向服务商注册的时候生成，一并生成的还有对应密钥

## OpenId

### 场景
- 向多实体认证自身时，关联的唯一账户
    - 参照于身份证的用途

### 流程
- ![OpenID.png](http://doc.yqjdcyy.com/9ed028d6-8f83-485e-9cc2-67e325932573.png)

### 注意事项
- 与 OAuth 的区别
    - OAuth 认证后，相关于给了你家里面的钥匙，这个可以取，那个可以改
    - OpenID 认证，则是告诉你 `我是我`
        - 可选择是否告诉你邮箱等信息

## SMAL 2.0

### 描述
- Security Assertion Markup Language
- 安全断言
    - XML 格式的语言

### 流程
- ![SAML.png](http://doc.yqjdcyy.com/3189c7d5-3193-4ce1-a754-4fbe5c17e218.png)

### 注意事项
- 通知第三方应用方式
    - 重定向
        - 不推荐
        - URL 长度限制，不便携带 SMAL Token
    - POST
        - 登录后通过「渲染表单|Javascript」向服务提供商提交 POST 请求

- 不适用于手机应用
    - 通过 URL 可以呼起应用，但其无法解析 XML 数据，获取


# 补充
## SSO
### 描述
- Single sign-on
- 同平台下各网站使用同一账号体系，当在某处登录后，即可访问其它所有网站
    - 解决方案

### 实现方案
- OAuth 2.0
- SMAL 2.0



# 参考
- [HTTP验证大法(Basic Auth,Session, JWT, Oauth, Openid)](https://segmentfault.com/a/1190000008481722#articleHeader19)
- [理解OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)
- [10 分钟理解什么是 OAuth 2.0 协议](https://deepzz.com/post/what-is-oauth2-protocol.html)
- [全面了解Token,JWT,OAuth,SAML,SSO](https://zhuanlan.zhihu.com/p/38942172)
- [OpenID](https://zh.wikipedia.org/wiki/OpenID)
- [OpenID 认证概述](https://www.ibm.com/support/knowledgecenter/zh/SSAW57_8.5.5/com.ibm.websphere.nd.multiplatform.doc/ae/csec_oiddesc.html)
- [OpenID 和 OAuth 有什么区别？](https://www.zhihu.com/question/19628327)