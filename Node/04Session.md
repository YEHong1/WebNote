## 1.简介

- Session 是另一种记录客户状态的机制，不同的是 Cookie 保存在客户端浏览器中，而 session 保存在服务器上。
- Session 数据放在服务器，Session 比 Cookie 要更安全一些。当访问量增多的时候，会比较占用服务器的性能。
- 单个 cookie 保存的数据大小不能超过 4K。Session 没有这方面的限制。
- Session 是基于 Cookie 进行工作的。



## 2.Session的工作流程

当浏览器访问服务器并发送第一次请求时，服务器端会创建一个 session 对象，生成一 个类似于 key,value 的键值对， 然后将 cookie 返回到客户端，浏览器下次再访问时，携带 cookie，找到对应的 session(value)。



## 3.Express中使用Session

```js
// 1.安装 express-session
npm i express-session --save

// 2.引入 express-session
let session = require("express-session");

// 3.作为中间件使用
app.use(session({
		secret: 'keyboard cat', resave: true, saveUninitialized: true
}))

// 4.使用
// 设置值 
req.session.username = "张三";
// 获取值 
req.session.username
```



## 4.express-session 的常用参数

| 属性              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| secret            | 一个 String 类型的字符串，作为服务器端生成 session 的签名。  |
| name              | 返回客户端的 key 的名称，默认为 connect.sid,也可以自己设置。 |
| resave            | 强制保存 session 即使它并没有变化。默认为 false。            |
| saveUninitialized | 强制将未初始化的 session 存储。当新建了一个 session 且未设定属性或值时，它就处于未初始化状态。在设定一个 cookie 前，这对于登陆验证，减轻服务端存储压力，权限控制是有帮助的。（默认：true）。建议手动添加。 |
| cookie            | 设置返回到前端 key 的属性                                    |
| rolling           | 在每次请求时强行设置 cookie，这将重置 cookie 过期时间（默认：false） |



## 5.express-session 的常用方法

```js
// 销毁 session
req.session.destroy(err =>{ 
  
})

// 设置 session
req.session.username = '张三';

// 获取 session
req.session.username

// 重新设置 cookie 的过期时间
req.session.cookie.maxAge=0;
```



## 6.负载均衡配置 Session，把 Session 保存到数据库里面

```js
// 1.需要安装 express-session 和 connect-mongo 模块
// 2.引入模块
let session = require("express-session");
const MongoStore = require('connect-mongo')(session);
// 3.配置中间件
app.use(session({
  secret: 'keyboard cat',
  resave: false,
  saveUninitialized: true,
  rolling:true,
  cookie:{
    maxAge:100000
  },
  store: new MongoStore({
    url: 'mongodb://127.0.0.1:27017/student',
    touchAfter: 24 * 3600 // time period in seconds
  })
}))
```

