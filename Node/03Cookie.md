## 1.简介

-    cookie 是存储于访问者的计算机中的变量。可以让我们用同一个浏览器访问同一个域名的时候共享数据。 
-    HTTP 是无状态协议。简单地说，当你浏览了一个页面，然后转到同一个网站的另一个页面，服务器无法认识到这是同一个浏览器在访问同一个网站。每一次的访问，都是没有任何关系的。 
-    Cookie 是一个简单的想法：当访问一个页面的时候，服务器在响应报文中， 命令浏览器存储一个字符串; 浏览器再访问同一个域的时候，将把这个字符串携带到请求中。第一次访问一个服务器，不可能携带 cookie。 必须是服务器得到这次请求， 在响应头中，携带 cookie 信息，此后每一次浏览器往这个服务器发出的请求，都会 携带这个 cookie。 



## 2.Cookie 特点 

-    cookie 保存在浏览器本地 
-    正常设置的 cookie 是不加密的，用户可以自由看到 
-    用户可以删除 cookie，或者禁用它 
-    cookie 可以被篡改 
-    cookie 可以用于攻击 
-    cookie 存储量很小



## 3.Cookie 的使用 

 Express 中要使用 Cookie 的话，我们需要使用 cookie-parser 模块来实现 

```js
// 1.安装 
npm instlal cookie-parser --save

// 2.引入 
var cookieParser = require('cookie-parser');

// 3.设置中间件
app.use(cookieParser());

// 4.设置 cookie
res.cookie("cookie_name",'cookie_value',{maxAge: 1000 * 60 * 60, httpOnly: true});

// 5. 获取 cookie
req.cookies['cookie_name']
```



###  Cookie 属性说明 

| 属性       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| `maxAge`   | 最大失效时间（毫秒），设置在多少毫秒后失效                   |
| `Expires`  | 过 期 时 间  ， 在 设 置 的 某 个 时 间 点 后 该 Cookie 就 会 失 效 ， 如 expires=Wednesday, 09-Nov-99 23:12:40 GMT |
| `secure`   | 当 secure 值为 true 时，cookie 在 HTTP 中是无效，在 HTTPS 中才有效 |
| `Path`     | 表示 cookie 影响到的路径，如 path=/。如果路径不能匹配时，浏览器则 不发送这个 Cookie |
| `httpOnly` | 是微软对 COOKIE 做的扩展。如果在 COOKIE 中设置了“`httpOnly`”属性，则通 过程序（JS 脚本、applet 等）将无法读取到 COOKIE 信息，防止 XSS 攻击产生 |
| `singed`   | 表示是否签名 cookie, 设为 true 会对这个 cookie 签名，这样就需要用 `res.signedCookies` 而不是 `res.cookies` 访问它。被篡改的签名 cookie 会被服务器拒绝，并且 cookie 值会重置为它的原始值 |
|            |                                                              |



### 加密cookie 实例

```js
// 1.安装 
npm instlal cookie-parser --save

// 2.引入 
var cookieParser = require('cookie-parser');

// 3.设置中间件, 需要添加签名
app.use(cookieParser('签名，可以随便写个英文字符串'));

// 4.设置 cookie
res.cookie("name",'zhangsan',{ maxAge: 1000 * 60 * 60, singed: true });

// 5. 获取 cookie
req.signedCookies.name
```

