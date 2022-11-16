

## 1.项目初始化并安装 `Koa`

```shell
npm init -y
npm i koa
```



## 2.创建一个`http`服务

```js
// 引入koa
const Koa = require("koa");
// 实例化koa
const app = new Koa();
// 注册中间件
app.use((ctx) => {
  // ctx 是 http 的上下文对象
  ctx.body = "hello koa";
});
// 启动服务，监听3000端口
app.listen(3000, () => {
  console.log("server is running in 3000");
});
```



## 3.中间件

有时候从请求到响应的业务比较复杂, 将这些复杂的业务拆开成一个个功能独立的**函数**, 就是**中间件**。
对于处理请求来说，在响应发出之前，可以在请求和响应之间做一些操作，并且可以将这个处理结果传递给下一个函数继续处理

```js
const Koa = require("koa");
const app = new Koa();

// app.use() 的返回值是this，所以可以像promise那样链式调用。 app.use().use()
app.use((ctx, next) => {
  console.log("step1");
  // 如果有下一个中间件需要执行，需要调用一下next
  next();
});

app.use((ctx, next) => {
  console.log("step2");
  next();
});

app.use((ctx, next) => {
  console.log("finish");
  ctx.body = "hello koa";
});

app.listen(3000, () => {
  console.log("server is running in 3000");
});
```















