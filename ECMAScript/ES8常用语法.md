## 1.  async 函数 

-    async 函数的返回值为 promise 对象
-    promise 对象的结果由 async 函数执行的返回值决定 

```js
// async函数的返回值为promise对象
// 1.函数内return 非promise对象值时，调用async函数返回一个 resolved（成功）状态的promise对象
let fn1 = async ()=> 1
// 相当于： let fn1 = async ()=> Promise.resolve(1)

// 2.函数内抛出错误，调用async函数返回一个 fulfilled（失败）状态的promise对象
let fn2 = async ()=> {
    throw new Error('模拟错误代码')
}
// 3.函数内返回一个promise对象时，
let fn3 = async ()=> {
    return new Promise((resolve, reject)=>{
        resolve('成功')
    })
}
// 调用async函数返回一个 fulfilled 状态的promise
console.log(fn3())

let fn4 = async ()=> {
    return new Promise((resolve, reject)=>{
        reject('失败')
    })
}
// 调用async函数返回一个 rejected 状态的promise
console.log(fn4())
```



## 2. await 表达式

-    await 必须写在 async 函数中 
-    await 右侧的表达式一般为 promise 对象 
-    await 返回的是 promise 成功的值 
-    await 的 promise 失败了, 就会抛出异常, 需要通过 try...catch 捕获处理 

```js
let p = new Promise((resolve, reject) => {
    reject('失败原因')
})
let fn1 = async ()=> {
    try{
        let result = await p;
        console.log(result)
    }catch (e){
        console.log(e)
    }
}

fn1()

// await 右侧不为 Promise对象时，也会自动把右侧转为Promise对象
let result = await 100;
// 相当于： let result = await Promise.resolve(100);

// 注意： 当 await 右侧为 失败状态的 Promise时， 后面的代码不会执行，需要加 try catch 捕获错误
;(async ()=>{
	let result = await Promise.reject(100); // 报错，不会执行后面的代码
	console.log(result)
})()

// await后面的代码是当成 回调函数来处理的
let fn1 = async ()=>{
	console.log('async fn1')
}

let fn2 = async ()=>{
	console.log('fn2 start');
	await fn1();
    // await后面的代码相当于回调函数，在event loop的机制下，所有同步代码执行完成后，才会去执行回调函数
    // 所以会在打印 end 后，才打印fn2 end
	console.log('fn2 end')
}
console.log('start')
fn2();
console.log('end')

// 打印顺序： 
// start
// fn2 start
// async fn1
// end
// fn2 end


```



## async 和 await 处理异步请求

```js
let sendAjax = ()=>{
	return new Promise((resolve, reject) => {
		setTimeout(()=>{
			Date.now() % 2 === 0 ? resolve('成功返回数据') : reject('失败返回原因')
		}, 3000)
	})
}

// async await配合使用将异步代码按同步执行
let fn = async ()=>{
	let result;
	try {
		result = await sendAjax();
	}catch (e) {
		console.log(e)
	}
	console.log(result);
}

fn();
```



## 3.对象扩展语法

```js
let school = {
	name: '北京朝阳小学',
	address: '中国北京市朝阳区'
}

// 查看对象 属性名 集合
console.log(Object.keys(school))
// 查看对象 属性值 集合
console.log(Object.values(school))

```

