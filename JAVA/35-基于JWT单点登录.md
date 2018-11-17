# JWT
> 写于：2018-11-09

### 登录验证的几种方式

- Session
> 最开始的是用session，将sessionId放到cookie中，保留到客户端，但是分布式环境下无法使用

- Token
> 将token保存在cookie中，每次请求把token从header中带回来，从redis中根据token查相关的用户信息，token没有加密，存在安全问题

- JWT
> Json Web Token 也是基于token的思想，但是它是升级版，保证token不会被篡改，可校验，具体可以看看[阮一峰](http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)写的介绍，**了解原理和它的数据结构**

### Java实现
[源码链接](https://github.com/liueleven/java-jwt)
