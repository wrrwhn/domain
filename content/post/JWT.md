---
title: "JWT"
date: "2018-03-09"
categories:
 - "整理"
tags:
 - "JWT"
toc: true
---


# 定义
```
Json web token (JWT), 是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准（(RFC 7519).该token被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）场景。JWT的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该token也可直接被用于认证，也可被加密。
```

# 实现
- 组成
	- `header.playload.signature`
	- header
		- typ
			- 声明类型
		- alg
			- 加密算法
				- RSA1_5
				- HS256
	- playload
		- 组成
			- Registered Claim Names
				- iss
					- Issuer
					- 该 JWT 的签发者
					- 可选项，大小写敏感
				- sub
					- Subject
					- 该JWT所面向的用户，局部上下文或全局唯一
					- 可选项，大小写敏感
				- aud
					- Audience
					- 接收该 JWT 的一方，指定应用方
					- 可选项，大小写敏感
				- exp
					- Expiration Time
					- 过期时间，Unix时间戳
					- 可选项
				- nbf
					- Not Before
					- 起始可用时间，Unix时间戳
				- iat
					- Issued At
					- 标记签发时间，Unix时间戳
					- 可选项
				- jti
					- JWT ID
					- 可选项
			- Public Claim Names
				- 可添加任意信息
					- 常用于培养出用户、业务相关信息
					- 不建议添加敏感信息
			- Private Claim Names
				- 提供者和消费者的共同声明
	- signature
		- encrypt(base64(header)+ '.'+ base64(playload), 私钥)
- 流程事例
	- 生成 header
		- 填充 header
			```
			{
			    'typ': 'JWT',
			    'alg': 'HS256'
			}
			```
		- 对 header 进行 base64 加密
			- `eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9`
	- 生成 playload
		- 填充 playload
			```
			{
			    "iss": "joe",
			    "exp": 1300819380,
			    "http://example.com/is_root": true
			}
			```
		- 对 playload 进行 base64 加密
			- `eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9`
	- 生成 signature
		- 拼接
			- {header}+ {playload}
			- `eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9`
		- 加密算法计算
			- HS256({header}+ {playload}, 私钥)
			- `TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ`
	- 结果
		- `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ`

# 应用
```
fetch('api/user/1', {
    headers: {
        'Authorization': 'Bearer ' + token
    }
})
```
- ![JWT流程.png](http://otzm88f21.bkt.clouddn.com/2c8aeed0-ced4-41df-8bc8-39d8cdb3e0e9.png)


# 优点
- 跨语言支持
- 无需在服务端保存会话，易于应用扩展
- 字节占用小，便于传输

# 对比
- session 认证
	- 实现
		- 登录后将相关信息保存于 cookie 中，便于下次请求时服务器的识别认证
	- 缺点
		- 服务器开销
			- 认证后均需于服务端记录（内存等），增加服务器开销
		- 扩展性
			- cookie 仅于当前服务器有效，分页性扩展差
				- 可通过 redis 等公用服务支持
		- CSRF
			- 跨站请求伪造
			- cookie 被截获，受到跨站请求伪造攻击
- token 鉴权
	- 流程
		- 用户使用账号、密码登录
		- 服务器验证用户信息
		- 验证通过后返回给用户以 token
		- 客户端存储 token，并于请求时附上
		- 服务端验证 token 并处理请求
	- 补充
		- 服务端需支持 CORS （跨域资源共享）
			- 配置 `Access-Control-Allow-Origin: *`


# 适用场景
- 一次性认证

# 参考
- [jwt 官网](https://jwt.io/)
- [JSON Web Token - 在Web应用间安全地传递信息](http://blog.leapoahead.com/2015/09/06/understanding-jwt/)
- [什么是 JWT -- JSON WEB TOKEN](https://www.jianshu.com/p/576dbf44b2ae)
- [dgrijalva/jwt-go](https://github.com/dgrijalva/jwt-go)
- [SermoDigital/jose](https://github.com/SermoDigital/jose)
- [JSON Web Token (JWT) draft-ietf-oauth-json-web-token-32](https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32)
- [Example JWT](https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32#section-3.1)
- [讲真，别再使用JWT了](http://insights.thoughtworkers.org/do-not-use-jwt-anymore/)
- [Go实战--golang中使用JWT(JSON Web Token)](https://studygolang.com/articles/10628)

