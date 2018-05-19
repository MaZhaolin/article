---
title: JSON WEB TOKEN
date: 2018-01-23 18:47:30
tags: jwt 
---



> jwt实现无状态权限验证，基于token的鉴权机制

# jwt 构成

头部 `header`  载荷 `payload` 签证 `signature`

<!-- more -->

## header

- 声明类型，此处JWT
- 声明加密类型 通常使用 HMAC SHA256

```json
{
  "typ": "JWT",
  "alg": "HS256"
}
```

对头部进行`base64`加密，构成第一部分

## playload

**载荷存放有效信息，含三个部分：**

- 标准中注册的声明
- 公共的声明
- 私有的声明

**标准中注册的声明(建议) ：**

- `iss`： jwt的签发者
- `sub`： jwt所面向的用户
- `aud`： 接受jwt的一方
- `exp`： jwt的过期时间，大于签发时间
- `nbf`： 在此时间之前，此jwt都是不可用的
- `iat`： jwt的签发时间
- `jti`： jwt的唯一身份标识

**公共的声明**：可添加任何信息，一般为用户信息，可在见客户端解密

**私有的声明**：提供者和消费者所公同定义的声明

payload例子：

````json
{
  "sub": "10001",
  "name": "Jack",
  "admin": "true"
}
````

对其进行base64加密，得到jwt的第二部分

## signature

**签证信息，三部分组成：**

- 加密后的base64
- 加密后的payload
- secret 由自己生成的密匙

示例代码

```Javascript
var header = JSON.stringify({
    typ: 'JWT',
    alg: 'SH256'
})
var playload = JSON.stringify({
    sub: 10001,
  	name: 'admin',
  	amdin: true
})
var secret = 'generate secret enter here'
var str = base64UrlEncode(header) + '.' + base64UrlEncode(playload)
var signature = HMACSH256(str, secret)
var jwt = `${header}.${playlaod}.${signature}`
```

# 应用

在请求头中加入`Authorization`，并加入`Bearer`标注：

```javascript
axios.create({
  withCredentials: true,
  headers: {
    'Authorization': 'Bearer' + token
  }
})
```



服务端接受请求时对`token`进行验证