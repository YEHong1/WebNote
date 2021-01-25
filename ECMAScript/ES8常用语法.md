## 1.  async 函数 

-    async 函数的返回值为 promise 对象
-    promise 对象的结果由 async 函数执行的返回值决定 

```js
// async函数的返回值为promise对象
// 1.函数内return 非promise对象值时，调用async函数返回一个fulfilled（成功）状态的promise对象
let fn1 = async ()=> 1
// 2.函数内抛出错误，调用async函数返回一个rejected（失败）状态的promise对象
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

12月 7号下班忘记打卡
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

