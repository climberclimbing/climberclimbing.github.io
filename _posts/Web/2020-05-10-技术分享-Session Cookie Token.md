---
layout: post
title: 20200510技术分享-Session、Cookie、Token
categories: Web
description: 
keywords: 
---

![Token 过期，刷新 Token](https:/climberclimbing.github.io/images/05.jpg)

## HTTP

HTTP是一种“无状态”协议。无状态协议不要求服务器在多个请求期间保留有关每个用户的信息或状态。
HTTP是无状态协议，这意味着一旦事务结束，浏览器和服务器之间的连接就会丢失
(就是客户端第二次来访时，服务器根本不知道这个客户端以前是否来访过，Web服务器本身不能识别出哪些请求是同一个浏览器发出的。即：浏览器的每一次请求都是完全孤立的)。
但是，一些Web应用程序需要在多个页面发送请求时跟踪用户状态(用户认证)，例如，当需要Web服务器为用户定制网页内容。解决办法包括：

1. Cookie

2. Session

3. Token

## Cookie

Cookie是由服务器端生成，发送给User-Agent（一般是web浏览器），浏览器会将Cookie的key/value保存到某个目录下的文本文件内，
下次请求同一网站时(Cookie也有跨域问题)就发送该Cookie给服务器（前提是浏览器设置为启用Cookie）。

Cookie处理流程：

1. 服务器像客户端发送cookie；

2. 浏览器将cookie保存；

3. 之后每次http请求浏览器都会将cookie发送给服务器端；

Cookie的两种类型：

1. 会话Cookie (Session Cookie):会话Cookie被保存在浏览器的内存中，当浏览器关闭时，内存被释放，内存中的Cookie自然也就不存在了。

2. 持久性Cookie (Persistent Cookie)

Cookie优缺点：

优点：

1. 可配置到期规则 Cookie 可以在浏览器会话结束时到期，或者可以在客户端计算机上无限期存在，这取决于客户端的到期规则；

2. 不需要任何服务器资源 Cookie 存储在客户端并在发送后由服务器读取；

3. 简单性 Cookie 是一种基于文本的轻量结构，包含简单的键值对；

缺点：

1. cookie的数量和长度都有限制；

2. 潜在的安全风险：cookie可能被截取篡改，如果cookie被拦截，就可能会获取到所有的Session信息(CSRF攻击[5])；

3. 用户配置为禁用，有的用户禁用了浏览器或者客户端设备接受cookie的能力，因此限制了这一功能(URL重写)；

4. 有些状态不可能保存在客户端，例如，为了防止重复提交表达，需要在服务器端保存一个计时器，如果把这个计时器保存在客户端，它将不起作用;

5. 受限于浏览器同源策略[8][9]；

Cookie典型应用

1. 记住密码

2. 购物车


## Session

Session是指一个终端用户与交互系统进行通信的时间间隔，通常指从登录进入系统到注销退出系统之间所经过的时间以及如果需要的话，可能还有一定的操作空间。
具体到Web中的Session指的就是用户在浏览某个网站时，从进入网站到浏览器关闭所经过的这段时间，也就是用户浏览这个网站所花费的时间。因此从上述的定义中我们可以看到，Session实际上是一个特定的时间概念。

当客户端访问服务器时，服务器根据需求设置Session，将会话信息保存在服务器上，同时将标示Session的SessionId传递给客户端浏览器，
浏览器将这个SessionId保存在内存中，我们称之为无过期时间的Cookie(Session底层依赖Cookie技术。可以通过其它方式解除这种依赖。见下文)。浏览器关闭后，这个Cookie就会被清掉，它不会存在于用户的Cookie临时文件。
以后浏览器每次请求都会额外加上这个参数值，服务器会根据这个SessionId，就能取得客户端的数据信息。

关于Session覆盖问题

同一个域下的单个应用Session可能在同一打开的浏览器的不同标签页相互覆盖；

同一个域下的多个应用Session可能会会互相覆盖[6]；

关于Session安全问题

基于Cookie机制的Session同样存在安全问题。但相对于直接将数据存储在客户端的Cookie要安全一些。

关于服务器集群Session共享的问题[10][11][12]

将应用系统部署到多台应用服务器上时，用户的请求也会通过负载均衡转发到某个具体应用服务器上执行，可能会出现在A1系统登录后创建并保存Session，
再次发起请求，请求被转发到A2系统上显示未登录的情况，此时单机部署模式下的Session机制已不能满足要求。


关于URL重写

如果浏览器禁用了cookie那么可以将jsessionid重写到URL里。格式如下http://ip:port/context/index?jsessionid=xxxxxxxxxxx?param1=param1&param2=param2 

实际上，有四种方式让Session正常工作：

1. 通过URL传递SessionID

2. 通过Cookie传递SessionID

3. 通过SSL传递SessionID

4. 通过隐藏表单传递SessionID


## Token

token的意思是“令牌”，是服务端生成的一串字符串，作为客户端进行请求的一个标识。
当用户第一次登录后，服务器生成一个token并将此token返回给客户端，以后客户端只需带上这个token前来请求数据即可，无需再次带上用户名和密码。

Token可以解决Cookie和Session存在的几个问题[13]：


1. Token 完全由应用管理，所以它可以避开同源策略；

2. Token 可以避免 CSRF 攻击；

3. Token 可以是无状态的，可以在多个服务间共享；

Token的时序图表示[14][15]：

![登录](https:/climberclimbing.github.io/images/01.webp)

![业务请求](https:/climberclimbing.github.io/images/02.webp)

![Token 过期，刷新 Token](https:/climberclimbing.github.io/images/03.webp)

如果我们把所有状态信息都附加在 Token 上，服务器就可以不保存。
但是服务端仍然需要认证 Token 有效。
不过只要服务端能确认是自己签发的 Token，而且其信息未被改动过，那就可以认为 Token 有效——“签名”可以作此保证。
平时常说的签名都存在一方签发，另一方验证的情况，所以要使用非对称加密算法。
但是在这里，签发和验证都是同一方，所以对称加密算法就能达到要求，而对称算法比非对称算法要快得多（可达数十倍差距）。
更进一步思考，对称加密算法除了加密，还带有还原加密内容的功能，而这一功能在对 Token 签名时并无必要(单纯获得一个Token，而不进行用户认证)

[JWT](https://jwt.io/)(JSON WEB TOKEN) 已经帮我们实现了以上过程。


## JWT(JSON WEB TOKEN)

Json web token (JWT), 是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准（(RFC 7519).
该token被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）场景。
JWT的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该token也可直接被用于认证，也可被加密。

简单说来JWT(JSON WEB TOKEN)就是符合一定规范的具有唯一标示性的Token字符串。

JWT长什么样？

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```
![JWT](https:/climberclimbing.github.io/images/04.png)

JWT标准的token包含三部分：

1. header(头部)，头部信息主要包括（参数的类型--JWT,签名的算法--HS256）；

```
{"typ":"JWT","alg":"HS256"}
```

2. poyload(负荷)，负荷基本就是自己想要存放的信息(因为信息会暴露，不应该在载荷里面加入任何敏感的数据)

```
{"sub":"1234567890","name":"John Doe","admin":true}
```

3. sign(签名)[16]，签名的作用就是为了防止恶意篡改数据；


JWT生成Token的步骤：

1. 对header(头部)进行base64加密，得到JWT的第一部分；

2. 对poyload(负荷)进行base64加密，得到JWT的第二部分；

3. 将前两部分得到的字符串用"."连接起来，然后通过header中声明的加密方式进行加盐secret组合加密；


关于Tkoen的安全性

无状态的JWT 把token返回给用户A，假如现在B用户盗用了这个token，B用户也能携带这个token去请求了。

针对这一问题，有多种解决方案：

1. 设置Token过期时间；

2. 生成token时，在payload里增加当前时间戳.后端接收后，对解析出来的时间戳和当前时间进行判断，如果相差特定时间内（比如2秒），允许请求否则判定为重复攻击；

3. 使用HTTS；

4. ……



## 参考

[1] [cookie安全性问题](https://blog.csdn.net/resilient/article/details/85249842)

[2] [Cookie和Session的使用和区别](https://www.jianshu.com/p/9a561b36e9f3)

[3] [各类JWT库(java)的使用与评价](http://andaily.com/blog/?p=956)

[4] [记一次token安全认证的实践](https://zhuanlan.zhihu.com/p/65042754)

[5] [cookie和CSRF、xss攻击](https://www.jianshu.com/p/78f65023cf9d)

[6] [应用的Session冲突](https://my.oschina.net/moyuqi/blog/98475)

[7] [为什么需要cookie和session](https://www.cnblogs.com/zwt0626/p/11803683.html)

[8] [浏览器同源策略及Cookie的作用域](https://www.cnblogs.com/liuqiyun/p/8663935.html)

[9] [同源策略和跨域解决方案](https://www.cnblogs.com/rain-chenwei/p/9520240.html)

[10] [如何实现session共享的几种解决方案？](https://blog.csdn.net/qq_36520235/article/details/87830929)

[11] [分布式Session解决方案](https://www.cnblogs.com/SimpleWu/p/10118674.html)

[12] [如何实现Session共享](https://www.iteye.com/blog/uule-2236466)

[13] [token和session的区别](https://www.cnblogs.com/belongs-to-qinghua/p/11353228.html)

[14] [OAuth 2.0 的一个简单解释](http://www.ruanyifeng.com/blog/2019/04/oauth_design.html)

[15] [关于token和refresh token](https://blog.csdn.net/weixin_41282397/article/details/81908025)

[16] [JWT-token—前后端分离架构的api安全问题](https://www.jianshu.com/p/836df92c06eb)

[17] [加盐加密的简单理解](https://blog.csdn.net/Sacredness/article/details/86027143)
