---
title: 【每周一文】使用JWT实现基于API的用户认证
date: 2017-04-06 21:58:08
tags: [JWT, API]
---

# 什么是 JWT？

JWT 全名 JSON Web Token，是 [Auth0](https://auth0.com/) 提出的通过对 JSON 进行加密签名来实现授权验证的方案，它是一种基于 Token 的认证方案。简单的说，JWT 就是一种 Token 的编码算法，应用流程大体如下：

> 1、客户端将用户名和密码以 HTTP POST 请求的方式发送给服务器。

> 2、服务器接受到用户名密码并验证通过后，用一个密钥生成 JWT，并将这个JWT 返回给客户端。

> 3、客户端将 JWT 保存在本地，每次在向服务器请求资源时把 JWT 包含在  HTTP 请求报文里面（authorization header 里面或者请求参数中），然后发送请求给服务器。

> 4、服务器可以在 JWT 中提取用户相关信息。进行验证。

> 5、服务器验证完成后，发送响应结果给客户端。

<!-- more --> 

# 为什么要用 JWT

JWT 只通过算法实现对 Token 合法性的验证，不依赖数据库，Memcached 的等存储系统，因此可以做到跨服务器验证，只要密钥和算法相同，不同服务器程序生成的 Token 可以互相验证。

使用 JWT 可以省去服务端读取 Session 的步骤，这样更符合 RESTful 的规范。但是对于客户端（或 App 端）来说，为了保存用户授权信息，仍然需要通过 Cookie 或类似的机制进行本地保存。因此 JWT 是用来取代服务端的 Session 而非客户端 Cookie 的方案。

# JWT 标准的设计

一个JWT实际上就是一个字符串，它由三部分组成：头部、载荷与签名。编码之后的JWT看起来是这样的一串字符：

    eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
    .eyJzdWIiOiIxIiwiaXNzIjoiaHR0cDpcL1wvbG9jYWxob3N0OjgwMDFcL2F1dGhcL2xvZ2luIiwiaWF0IjoxNDUxODg4MTE5LCJleHAiOjE0NTQ1MTYxMTksIm5iZiI6MTQ1MTg4ODExOSwianRpIjoiMzdjMTA3ZTQ2MDlkZGJjYzljMDk2ZWE1ZWU3NmM2NjcifQ
    .wyoQ95RjAyQ2FF3aj8EvCSaUmeP0KUqcCJDENNfnaT4  

由.分为三段，下面我们具体讲解这三段字串：



### 一、头部

JWT 需要一个头部，头部用于描述关于该JWT的最基本的信息，例如其类型以及签名所用的算法等。这也可以被表示成一个JSON对象：

```json
{
  "typ": "JWT",
  "alg": "HS256"
}
```

将上面的JSON对象进行base64编码可以得到下面的字符串：

    eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9

这个字符串我们将它称作 JWT 的 Header（头部）。

### 二、载荷（Payload）

```json
{
    "sub": "1",
    "iss": "http://localhost:8000/auth/login",
    "iat": 1451888119,
    "exp": 1454516119,
    "nbf": 1451888119,
    "jti": "37c107e4609ddbcc9c096ea5ee76c667"
}
```
这里面的前6个字段都是由JWT的标准所定义的。

    sub: 该JWT所面向的用户
    iss: 该JWT的签发者
    iat(issued at): 在什么时候签发的token
    exp(expires): token什么时候过期
    nbf(not before)：token在此时间之前不能被接收处理
    jti：JWT ID 为web token提供唯一标识

将上面的JSON对象进行base64编码可以得到下面的字符串：

    eyJzdWIiOiIxIiwiaXNzIjoiaHR0cDpcL1wvbG9jYWxob3N0OjgwMDFcL2F1dGhcL2xvZ2luIiwiaWF0IjoxNDUxODg4MTE5LCJleHAiOjE0NTQ1MTYxMTksIm5iZiI6MTQ1MTg4ODExOSwianRpIjoiMzdjMTA3ZTQ2MDlkZGJjYzljMDk2ZWE1ZWU3NmM2NjcifQ

这个字符串我们将它称作 JWT 的 Payload（载荷）。

### 三、签名

将上面的两个编码后的字符串都用句号.连接在一起（头部在前），就形成了：

eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxIiwiaXNzIjoiaHR0cDpcL1wvbG9jYWxob3N0OjgwMDFcL2F1dGhcL2xvZ2luIiwiaWF0IjoxNDUxODg4MTE5LCJleHAiOjE0NTQ1MTYxMTksIm5iZiI6MTQ1MTg4ODExOSwianRpIjoiMzdjMTA3ZTQ2MDlkZGJjYzljMDk2ZWE1ZWU3NmM2NjcifQ

最后，我们将上面拼接完的字符串用HS256算法进行加密。在加密的时候，我们还需要提供一个密钥（ secret ）:

    HMACSHA256(
        base64UrlEncode(header) + "." +
        base64UrlEncode(payload),
        secret
    )

这样就可以得到我们加密后的内容：

    wyoQ95RjAyQ2FF3aj8EvCSaUmeP0KUqcCJDENNfnaT4

这一部分又叫做签名。
 
> 完整的 JWT

最后将这一部分签名也拼接在被签名的字符串后面，我们就得到了完整的 JWT：

    eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
    .eyJzdWIiOiIxIiwiaXNzIjoiaHR0cDpcL1wvbG9jYWxob3N0OjgwMDFcL2F1dGhcL2xvZ2luIiwiaWF0IjoxNDUxODg4MTE5LCJleHAiOjE0NTQ1MTYxMTksIm5iZiI6MTQ1MTg4ODExOSwianRpIjoiMzdjMTA3ZTQ2MDlkZGJjYzljMDk2ZWE1ZWU3NmM2NjcifQ
    .wyoQ95RjAyQ2FF3aj8EvCSaUmeP0KUqcCJDENNfnaT4

# 你可能遇到的问题


JWT Token 需要持久化在 Memcached 中吗？
    不应该这样做，这样就背离了JWT通过算法验证的初心。

在退出登录时怎样实现 JWT Token 失效呢？
    退出登录， 只要客户端端把 Token 丢弃就可以了，服务器端不需要废弃 Token。

怎样保持客户端长时间保持登录状态？
    服务器端提供刷新 Token 的接口， 客户端负责按一定的逻辑刷新服务器 Token。

服务器端是否应该从 JWT中 取出 user_id 用于业务查询？

    REST API 是无状态的，意味着服务器端每次请求都是独立的，即不依赖以前请求的结果，因此也不应该依赖 JWT token 做业务查询， 应该在请求报文中单独加个 user_id 字段。为了做用户水平越权的检查，可以在业务层判断传入的 user_id 和从 JWT token 中解析出的 user_id 是否一致， 有些业务可能会允许查不同用户的数据。


# Laravel 框架 JWT 实战

未完待续……










