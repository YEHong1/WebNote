## 1.介绍 Promise

```js
// 什么是promise
1.Promise是es6的语法，是js中进行异步编程的新的解决方案，promise可以链式调用，避免了回调地狱问题
2.从语法上来说：Promise是一个构造函数，
  从功能上来说：promise实例对象用来封装异步操作，并获得异步操作执行结果（成功或失败）值
```

```js
// 描述promise的状态
promise共有3种状态： pending(初始化)	fulfilled(成功，这个状态也称为resolved)	rejected(失败)
注意： 一个promise的状态只能改变一次，只会从pending变为fulfilled 或 pending变为rejected
```



## 2.Promise的基本使用

```js
let p = new Promise((resolve, reject) => {
	console.log('promise是同步执行的');
	// 异步操作
	setTimeout(()=>{
		let time = Date.now();
		time % 2 === 0 ?
			// 成功执行resolve()，promise状态从pending变为fulfilled， resolve函数可以接受参数进行传值
			resolve(`success ${time}`) :
			// 失败执行reject()，promise状态从pending变为rejected， reject函数可以接受参数进行传值
			reject(`fail ${time}`);
	}, 1000)
})

console.log('Hello Promise')

// promise.then() 可以接受两个函数作为参数，当promise状态变为fulfilled执行第一个函数, promise状态变为rejected执行第二个函数

// p.then(res=>{
// 	console.log(res)
// }, reason => {
// 	console.log(reason)
// })

// promise.catch() 接受一个函数，当promise状态变为rejected时执行
p.then(res=>{
	console.log(res)
}).catch(err=>{
	console.log(err)
})


// 执行代码，打印顺序为：
1.promise是同步执行的
2.Hello Promise
3.success 1610002610426 或 fail 1610002610425


// 注意 .then()和.catch()都会返回一个 fulfilled 状态的 promise， 在他们的函数体中抛出错误，则会返回一个rejected 状态的 promise
Promise.resolve().then(()=>{
	console.log(1)
}).catch(()=>{
	console.log(2)
}).then(()=>{
	console.log(3)
})
// 打印 1 3

// ==============================================================

Promise.resolve().then(()=>{
	console.log(1)
}).catch(()=>{
	console.log(2)
}).then(()=>{
    // 执行这句代码后，返回一个 rejected 状态的promise
	throw new Error('error')
}).catch(err=>{
	console.log('catch2被执行')
})
// 打印 1 catch2被执行

// ==============================================================

Promise.reject().then(()=>{
	console.log(1)
}).catch(()=>{
	console.log(2)
}).then(()=>{
	console.log(3)
})
// 打印 2 3

// ==============================================================

Promise.reject().then(()=>{
	console.log(1)
}).catch(()=>{
    console.log(2)
	throw new Error('error')
}).then(()=>{
	console.log(3)
}).catch(()=>{
	console.log(4)
})
// 打印 2 4



```



## 3.Api及关键问题

```js
1.Promise构造函数 new Promise(excutor)
  (1) excutor函数：执行器 (resolve, reject) => {}
  (2) resolve 函数: 内部定义成功时我们调用的函数 value => {}
  (3) reject 函数: 内部定义失败时我们调用的函数 reason => {}
  说明: executor 会在 Promise 内部立即同步调用,异步操作在执行器中执行

2.Promise.prototype.then  : (onResolved, onRejected) => {}
  (1) onResolved 函数: 成功的回调函数 (value) => {}
  (2) onRejected 函数: 失败的回调函数 (reason) => {}
  说明: 指定用于得到成功 value 的成功回调和用于得到失败 reason 的失败回调返回一个新的 promise 对象

3.Promise.prototype.catch : (onRejected) => {}
  (1) onRejected 函数: 失败的回调函数 (reason) => {}
  说明: then()的语法糖, 相当于: then(undefined, onRejected)

4.Promise.resolve : (value) => {}
  (1) value: 成功的数据或 promise 对象
  说明: 返回一个成功 promise 对象
  
5.Promise.reject : (reason) => {}
  (1) reason: 失败的原因
  说明: 返回一个失败的 promise 对象
  
6.Promise.all : (promises) => {}
  (1) promises: 包含 n 个 promise 的数组
  说明: 返回一个新的 promise, 只有所有的 promise 都成功才成功, 只要有一个失败了就直接失败

7.Promise.race : (promises) => {}
  (1) promises: 包含 n 个 promise 的数组
  说明: 返回一个新的 promise, 第一个完成的 promise 的结果状态就是最终的结果状态
```



```js
// 1.如何改变 Promise 的状态
(1) resolve(value): 如果当前是 pending 就会变为 fulfilled
(2) reject(reason): 如果当前是 pending 就会变为 rejected
(3) 抛出异常: 如果当前是 pending 就会变为 rejected
```

```js
// 2.一个 promise 指定了多个成功/失败回调函数, 都会调用吗?
(1) 当 promise 的状态发生改变后，对应状态的回调函数都会执行

let p = new Promise((resolve)=>{
	resolve('ok')
})

// 回调1
p.then(value => {
	console.log('回调1执行')
})
// 回调2
p.then(value => {
	console.log('回调2执行')
})

// 打印： 回调1执行 回调2执行
```

```js
// 3.改变 promise 状态和指定回调函数谁先谁后?
这个问题问的是： promise.resolve()先执行 还是 promise.then()先执行
(1) 都有可能, 如果promise的执行器里面的代码是同步代码，那么resolve先执行。如果是异步代码 then()先执行
(2) 什么时候才能得到数据?
    1.如果先指定的回调（ then() ）, 那当状态发生改变时, 回调函数就会调用, 得到数据
    2.如果先改变的状态, 那当指定回调时, 回调函数就会调用, 得到数据

// resolve先执行
let p1 = new Promise((resolve)=>{
	resolve('ok')
})

// promise.then()定义回调函数
p1.then(value => {
	console.log('ok')
})


// then 先执行
let p2 = new Promise((resolve)=>{
	setTimeout(()=>{
		resolve('ok')
	}, 1000)
})

// promise.then()定义回调函数
p2.then(value => {
	console.log('ok')
})

```

```js
// 4.promise.then()返回的新 promise 的结果状态由什么决定?
(1) 如果抛出异常, 新 promise 变为 rejected, reason 为抛出的异常
(2) 如果返回的是非 promise 的任意值, 新 promise 变为 resolved, value 为返回的值
(3) 如果返回的是另一个新 promise, 此 promise 的结果就会成为新 promise 的结果
```

```js
// 5.promise 如何串连多个操作任务?
(1) promise 的 then()返回一个新的 promise, 可以写成多个then()的链式调用
(2) 通过 then 的链式调用串连多个同步/异步任务

let p = new Promise(resolve => {
	setTimeout(()=>{
		resolve(1)
	}, 3000)
})

p.then(value => {
	console.log(value);
	return new Promise(resolve => {
		setTimeout(()=>{
			resolve(2)
		}, 1000)
	})
}).then(value => {
	console.log(value)
})

// 3s后打印1，再过1s后打印2
```

```js
// 6.promise 异常处理
当我们使用多个then()进行链式调用时，我们不想给每一个promise都定义失败的回调，那么我们可以在最后加上catch()
catch()可以接收到前面失败状态传递的结果值

let p = new Promise((resolve, reject) => {
	setTimeout(()=>{
		reject('第一个promise调用了reject')
	}, 3000)
})

p.then(value => {
	console.log(1)
}).then(value => {
	console.log(2)
}).catch(err=>{
	console.log(err)
})

// 打印 第一个promise调用了reject
```

```js
// 7.如何中断 promise 链
需求：当使用 promise 的 then 链式调用时, 在中间中断, 不再调用后面的回调函数
办法: 在回调函数中返回一个 pendding 状态的 promise 对象

let p = new Promise((resolve, reject) => {
	resolve()
})

p.then(value => {
	console.log(1)
}).then(value => {
	console.log(2)
	return new Promise(()=>{

	})
}).then(value => {
	console.log(3)
})

// 打印 1 2，没打印3
```

