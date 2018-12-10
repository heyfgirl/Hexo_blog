---
title: node安全性介绍
date: 2018-09-25 20:53:30
tags:
---
安全性，总是一个不可忽视的问题。许多人都承认这点，但是却很少有人真的认真地对待它。所以我们列出了这个清单，让你在将你的应用部署到生产环境来给千万用户使用之前，做一个安全检查。

以下列出的安全项，大多都具有普适性，适用于除了Node.js外的各种语言和框架。但是，其中也包含一些用Node.js写的小工具。

### 配置管理 ###
安全性相关的HTTP头
以下是一些安全性相关的HTTP头，你的站点应该设置它们：

- Strict-Transport-Security：强制使用安全连接（SSL/TLS之上的HTTPS）来连接到服务器。
- X-Frame-Options：提供对于“点击劫持”的保护。
- X-XSS-Protection：开启大多现代浏览器内建的对于跨站脚本攻击（XSS）的过滤功能。
- X-Content-Type-Options： 防止浏览器使用MIME-sniffing来确定响应的类型，转而使用明确的content-type来确定。
- Content-Security-Policy：防止受到跨站脚本攻击以及其他跨站注入攻击。

在Node.js中，这些都可以通过使用Helmet模块轻松设置完毕：

```javascript
var express = require('express');
var helmet = require('helmet');
var app = express();
app.use(helmet());
```
Helmet在Koa中也能使用：koa-helmet。

当然，在许多的架构中，这些头会在Web服务器(Apache，nginx)的配置中设置，而不是在应用的代码中。如果是通过nginx配置，配置文件会类似于如下例子：

```
# nginx.conf
add_header X-Frame-Options SAMEORIGIN;
add_header X-Content-Type-Options nosniff;
add_header X-XSS-Protection "1; mode=block";
add_header Content-Security-Policy "default-src 'self'";
```
完整的例子可以参考[这个nginx配置](https://gist.github.com/plentz/6737338)。

如果你想快速确认你的网站是否都设置这些HTTP头，你可以通过这个网站在线检查：[http://cyh.herokuapp.com/cyh](http://cyh.herokuapp.com/cyh) 。

### 客户端的敏感数据 ###
当部署前端应用时，确保不要在代码中暴露如密钥这样的敏感数据，这将可以被所有人看到。

现今并没有什么自动化检测它们的办法，但是还是有一些手段可以用来减少不小心将敏感数据暴露在客户端的概率：

- 使用pull request更新代码
- 建立起code review机制

### 身份认证 ###
对于暴力破解的保护
暴力破解即系统地列举所有可能的结果，并逐一尝试，来找到正确答案。在web应用中，用户登陆就特别适合它发挥。

你可以通过限制用户的连接频率来防止这类的攻击。在Node.js中，你可以使用[ratelimiter](https://www.npmjs.com/package/ratelimiter)包。
```javascript
var email = req.body.email;
var limit = new Limiter({ id: email, db: db });
limit.get(function(err, limit) {
});
```
当然，你可以将它封装成一个中间件以供你的应用使用。Express和Koa都已经有现成的不错的中间件：
```javascript
var ratelimit = require('koa-ratelimit');
var redis = require('redis');
var koa = require('koa');
var app = koa();
var emailBasedRatelimit = ratelimit({
  db: redis.createClient(),
  duration: 60000,
  max: 10,
  id: function (context) {
    return context.body.email;
  }
});
var ipBasedRatelimit = ratelimit({
  db: redis.createClient(),
  duration: 60000,
  max: 10,
  id: function (context) {
    return context.ip;
  }
});
app.post('/login', ipBasedRatelimit, emailBasedRatelimit, handleLogin);

```
这里我们所做的，就是限制了在一段给定时间内，用户可以尝试登陆的次数 – 这减少用户密码被暴力破解的风险。以上例子中的选项都是可以根据你的实际情景所改变的，所以不要简单的复制粘贴它们。。

如果你想要测试你的服务在这些场景下的表现，你可以使用[hydra](https://github.com/vanhauser-thc/thc-hydra)。
### Session管理 ###
对于cookie的安全使用，其重要性是不言而喻的。特别是对于动态的web应用，在如HTTP这样的无状态协议的之上，它们需要使用cookie来维持状态。

Cookie标示
以下是一个每个cookie可以设置的属性的列表，以及它们的含义：
- secure - 这个属性告诉浏览器，仅在请求是通过HTTPS传输时，才传递cookie。
- HttpOnly - 设置这个属性将禁止javascript脚本获取到这个cookie，这可以用来帮助防止跨站脚本攻击。

### Cookie域 ###
- domain - 这个属性用来比较请求URL中服务端的域名。如果域名匹配成功，或这是其子域名，则继续检查path属性。
- path - 除了域名，cookie可用的URL路径也可以被指定。当域名和路径都匹配时，cookie才会随请求发送。
- expires - 这个属性用来设置持久化的cookie，当设置了它之后，cookie在指定的时间到达之前都不会过期。

在Node.js中，你可以使用cookies包来轻松创建cookie。但是，它是较底层的。在创建应用时，你可能更像使用它的一些封装，如[cookie-session](https://www.npmjs.com/package/cookie-session) 。

```javascript
var cookieSession = require('cookie-session');
var express = require('express');
var app = express();
app.use(cookieSession({
  name: 'session',
  keys: [
    process.env.COOKIE_KEY1,
    process.env.COOKIE_KEY2
  ]
}));
app.use(function (req, res, next) {
  var n = req.session.views || 0;
  req.session.views = n++;
  res.end(n + ' views');
});
app.listen(3000);

```
### CSRF ###

跨站请求伪造（CSRF）是一种迫使用户在他们已登录的web应用中，执行一个并非他们原意的操作的攻击手段。这种攻击常常用于那些会改变用户的状态的请求，通常它们并不窃取数据，因为攻击者并不能看到响应的内容。

在Node.js中，你可以使用[csrf](https://www.npmjs.com/package/csrf)模块来缓和这种攻击。它同样是非常底层的，你可能更喜欢使用如csurf这样的Express中间件。

在路由层，可以会有如下代码：
```javascript
var cookieParser = require('cookie-parser');
var csrf = require('csurf');
var bodyParser = require('body-parser');
var express = require('express');
// setup route middlewares
var csrfProtection = csrf({ cookie: true });
var parseForm = bodyParser.urlencoded({ extended: false });
// create express app
var app = express();
// we need this because "cookie" is true in csrfProtection
app.use(cookieParser());
app.get('/form', csrfProtection, function(req, res) {
  // pass the csrfToken to the view
  res.render('send', { csrfToken: req.csrfToken() });
});
app.post('/process', parseForm, csrfProtection, function(req, res) {
  res.send('data is being processed');
});

```
在展示层，你需要使用CSRF token：
```bash
<form action="/process" method="POST">
  <input type="hidden" name="_csrf" value="{{csrfToken}}">
  Favorite color: <input type="text" name="favoriteColor">
  <button type="submit">Submit</button>
</form>

```
### 数据合法性 ###
以下是两种类似的，但是略有不同的攻击方式，一种关于跨站脚本，而另一种则关于存储。
- 非持久化的XSS攻击 在攻击者向指定的URL的响应HTML中注入可执行的JavaScript代码时发生。
- 持久化的XSS攻击 在应用存储未经过滤的用户输入时发生。用户输入的代码会在你的应用环境下执行。
为了防御这类攻击，请确保你总是检查并过滤了用户的输入内容。

### SQL注入 ###
在用户的输入中包含部分或完整的SQL查询语句时，SQL注入就有可能发生。它可能会读取敏感数据，或是直接删除数据。

例如：

- select title, author from books where id=$id

以上这个例子中，$id来自于用户输入。用户输入2 or 1=1也可以。这个查询可能会变成：

- select title, author from books where id=2 or 1=1

最简单的预防方法则是使用参数化查询（parameterized queries）或预处理语句（prepared statements）。
如果你正在通过Node.js使用PostgreSQL。那么你可以使用[node-postgres](https://www.npmjs.com/package/pg)模块，来创建参数化查询：

```bash
var q = 'SELECT name FROM books WHERE id = $1';
client.query(q, ['3'], function(err, result) {});
```
### 命令注入 ###
攻击者使用命令注入来在远程web服务器中运行系统命令。通过命令注入，攻击者甚至可以取得系统的密码。

实践中，如果你有一个URL：

- https://example.com/downloads?file=user1.txt

它可以变成：

- https://example.com/downloads?file=%3Bcat%20/etc/passwd

在这个例子中，%3B会变成一个分号。所以将会运行多条系统命令。
为了预防这类攻击，请确保总是检查过滤了用户的输入内容。
我们也可以以Node.js的角度来说：

```javascript
child_process.exec('ls', function (err, data) {
    console.log(data);
});
```
在child_process.exec的底层，它调用了/bin/sh，所以它是一个bash解释器，而不仅仅是只能执行用户程序。
当用户的输入是一个反引号或$()时，将它们传入这个方法就很危险了。
可以通过使用child_process.execFile来解决上面这个问题。

### 安全传输 ###
SSL版本，算法，键长度
由于HTTP是明文传输的，所以我们需要通过一个SSL/TLS通道来加密，即HTTPS。如今高级别的加密方式已被普遍使用，但是，如果在服务端缺乏配置，也可能会导致服务端使用低级别的加密，或不加密。

你需要测试：
密码，密钥和重协商（renegotiation）都已经合法妥善得配置完毕。
证书的合法性。
使用如nmap和sslyze这样的工具可以使这项工作非常简单。

检查证书信息

- nmap --script ssl-cert,ssl-enum-ciphers -p 443,465,993,995 www.example.com

使用sslyze来检查SSL/TSL：

- ./sslyze.py --regular example.com:443

HSTS
在上文的配置管理章节我们已经对其有了接触 - Strict-Transport-Security头会强制使用HTTPS来连接服务器。以下是一个Twitter的例子：

- strict-transport-security:max-age=631138519

这里的max-age定义了浏览器需要自动将所有HTTP请求转换成HTTPS的秒数。

对于它的测试是非常简单的：

- curl -s -D- https://twitter.com/ | grep -i Strict

### 拒绝服务 ###

账号锁定
账号锁定用于缓和暴力破解带来的拒绝服务方面的影响。实践中，它意味着，当用户尝试了几次登陆并失败后，将在其后的一段内，禁止他的登陆操作。

可以使用之前提到的rate-limiter来阻止这类攻击。

正则表达式
这类攻击主要是由于一些正则表达式，在极端情况下，会变得性能及其糟糕。这些正则被称为恶魔正则（Evil Regexes）：

- 对于重复文本进行分组
- 在重复的分组内又有重复内容([a-zA-Z]+)*， (a+)+ 或 (a|a?)+在如aaaaaaaaaaaaaaaaaaaaaaaa! 这样的输入面前，都是脆弱的。这会引起大量的计算。更多详情可以参考ReDos。

可以使用Node.js工具safe-regex这检测你的正则：

```
$ node safe.js '(beep|boop)*'
true
$ node safe.js '(a+){10}'
false
```

### 错误处理 ###

错误码，堆栈信息
一些错误场景可能会导致应用泄露底层的应用架构信息，如：like: X-Powered-By:Express。

堆栈信息可能自己本身并没有什么用，但它经常能泄露一些攻击者非常感兴趣的信息。将堆栈信息返回出来是非常不好的实践。你需要将它们记录在日志中，而不是展示给用户。

NPM
更强的能力意味着更大的责任 - NPM有这许多可以现成使用的包，但是代价是：你需要检查这些包本身是否存在安全问题。

幸运的是Node Security project(nsp)是一个非常棒的工具，来检查你使用的模块是否是易被一些已知的手段攻击的。
```
npm i nsp -g
# either audit the shrinkwrap
nsp audit-shrinkwrap
# or the package.json
nsp audit-package

```
最后
这个清单主要根据OWASP维护的[Web Application Security Testing Cheat Sheet](https://www.owasp.org/index.php/Web_Application_Security_Testing_Cheat_Sheet)所列。

