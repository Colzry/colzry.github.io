---
title: "JWT认证"
description: "JWT认证"
keywords: "JWT"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 认证和加密
tags:
  - JWT
---

# JWT认证

## 1. 什么是JWT

JSON Web Token（JWT）是一个开放的行业标准（RFC 7519），它定义了一种简洁的、自包含的协议格式，用于在通信双方传递json对象，传递的信息经过数字签名可以被验证和信任。JWT可以使用HMAC算法或使用RSA的公钥/私钥对来签名，防止被篡改。

## 2. JWT的优缺点

> 优点

1.  快速
2.  无状态
3.  多个服务端认证

> 缺点

1. 密钥泄漏风险高
2. 令牌容易被窃取

## 3. JWT的结构

官网链接：[jwt.io](https://jwt.io/)

JWT令牌由Header、Payload、Signature三部分组成，每部分中间使用点（.）分隔，例如
```bash
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.cThIIoDvwdueQB468K5xDc5633seEFoqwxjF_xSJyQQ
```

### 3.1 Header

头部包括令牌的类型（即JWT）及使用的加密算法（如HS256或RS256）。
例如：
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
HS256就是HMAC-SHA256，加密算法使用HMAC，摘要算法使用SHA256。
将上边的内容使用Base64Url编码，得到一个字符串就是JWT令牌的第一部分。

### 3.2 Payload

第二部分是负载，内容也是一个json对象，它是存放有效信息的地方，它可以存放jwt提供的现成字段，比 如：iss（签发者）,exp（过期时间戳）, sub（面向的用户）等，也可自定义字段。 此部分不建议存放敏感信息，因为此部分可以解码还原原始内容。
例如：
```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```
将第二部分负载使用Base64Url编码，得到一个字符串就是JWT令牌的第二部分。

> 其中官方将第二部分为三个内容
- Registered claims:
  官方预定义的、非强制性的但是推荐使用的、有助于交互的声明(注意使用这些声明只能是三个字符)。
  ![img](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/380ac6ddd91d64de42b72d07ce88582f.png)
- Public claims：
  保留给 JWT 的使用者自定义。但是需要注意避免使用IANA JSON Web Token Registry中定义的关键字。
- Private claims:
   保留给 JWT 的使用者自定义，用来传送传输双方约定好的消息。

### 3.3 Signature

第三部分是签名，此部分用于防止jwt内容被篡改。 这个部分使用base64url将前两部分进行编码，编码后使用点（.）连接组成字符串，最后使用header中声明 签名算法进行签名。 一个例子：

```bash
 HMACSHA256(
 base64UrlEncode(header) + "." +
 base64UrlEncode(payload),
 secret)
```
 其中 `secret` 可以为自定义的加密盐

base64UrlEncode(header)：jwt令牌的第一部分。 base64UrlEncode(payload)：jwt令牌的第二部分。 secret：签名所使用的密钥。

>**注意： 第三部分只是为了防止JWT的内容被篡改，并不能存入有效的信息**
