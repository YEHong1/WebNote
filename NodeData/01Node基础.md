

## 1.`Node`程序传递参数

假如当前有一个 `index.js`文件，我们可以通过 `node index.js`来执行这个文件。如果我们需要这个文件在不同的情况下产生不同的结果，我们就需要给它传递参数。

```js
// 传递参数的方式很简单，只需要在执行命令后追加想要传递的参数即可
node index.js YEHong env=development
// 那么如何在代码中获取这些参数呢？答案是Node的内置对象 process
console.log(process.argv);
```

![](./image/node_01.png)



## 2.`Node`中的全局对象

- `__dirname`：获取当前文件的路径
- `__filename`：获取当前文件的路径和文件名称
- `process`：process提供了Node进程中相关的信息
- `console`：提供了简单的调试控制台
- 定时器函数
- `exports、module、require()、Buffer`

### `global`对象

`global`是一个全局对象，事实上前端我们提到的`process、console、setTimeout`等都有被放到`global`中。它和 `window`的区别在于，`window`只存在于浏览器中。



## 3.内置模块 `path`

我们知道，在 `Mac、Linux、Window`这些不同的操作系统中，文件路径的拼接方式是不一样的。`Path`模块就是用于对路径和文件进行处理的，不需要开发者去根据系统环境来进行不同的路径拼接工作。

```js
// 常用 Api

```

























