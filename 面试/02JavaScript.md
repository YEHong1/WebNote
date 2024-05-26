## 1.数据类型有哪些

```js
// js目前有8中数据类型（还有一种是 bigInt）
let a; // undefined
const b = 1; // Number
const c = 'hello'; // String
const d = null; // null
const e = true; // Boolean
const f = Symbol('123'); // Symbol
const obj = { name: '张三', age: 18 }; // Object

// Object类型包含 对象、数组和函数
// Object是引用数据类型
// null是一个特殊的引用数据类型，它的值指向一个空地址
```



## 2.引用数据类型和基本数据类型的区别

原始数据类型：                                                                                                                                                                                                                                                                       

- 直接存储在**栈**（stack）中，占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储。

引用数据类型：

- 同时存储在**栈**（stack）和**堆**（heap）中，占据空间大、大小不固定。
- 引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。
- 当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。



## 3.`typeof` 能判断什么类型

-   能识别除了`null`以外的所有的基本数据类型
-   能够识别是不是函数
-   能够识别是否是引用数据类型（**但不能识别是对象还是数组**）

```js
console.log(typeof undefined) // undefined
console.log(typeof 1) // number
console.log(typeof 'hello world') // string
console.log(typeof true) // boolean
console.log(typeof Symbol('123')) // symbol
console.log(typeof function sayHello() {}) // function
console.log(typeof null) // object
console.log(typeof []) // object
console.log(typeof {}) // object

// 需要判断是不是数组用 instanceof， instanceof是基于原型来判断的
// 这里就是判断 [] 是不是 Array构造函数的实例
console.log([] instanceof Array) // true
```



## 4.深拷贝

```js
function deepClone(obj = {}){
	// 如果接收的参数是 基本数据类型 或者 null，不处理，直接返回
	if(typeof obj !== 'object' || obj == null){
		return obj;
	}
	// 判断接收的类型是数组还是对象，初始化返回结果
	let result = obj instanceof Array ? [] : {};
	// 数组和对象都有 for in 方法
	for(let key in obj){
		// 判断key是obj自身的属性，而不是原型的属性
		if(obj.hasOwnProperty(key)){
			// 递归调用，处理深层结构
			result[key] = deepClone(obj[key])
		}
	}
	// 返回结果
	return result;
}
```



## 5. == 与 ===

```js
// 使用 == 会将比较的两个值进行类型转换
// obj.a == null 相当于 obj.a === null || obj.a === undefined

// 除了 == null 外，一律推荐使用 ===
```



## 6.`turely`变量和`falsely`变量

```js
// turely变量： !!a === true a就是一个turyly变量
// falsely变量: !!b === false b就是一个falsely变量
```



## 7.原型和原型链



## 8.闭包

### 什么是闭包

 闭包是指有权访问另外一个函数作用域中的变量的函数。  可以理解为 闭包就是能够读取其他函数内部变量的函数 。

闭包主要有两种表现：函数作为参数被传递、函数作为返回值被返回

```js
function outer(){
	let name = '张三';
	let age = 18;
	let sayHello = function () {
		console.log(name, age);
	}
	// sayHello 就是一个闭包，因为它可以访问outer函数的作用域
	return sayHello;
}
```



### 闭包的作用

 闭包的最大用处有两个：一个是可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。 

```js
let add;
function f1(){
	let num = 1;
    // add是个全局变量，被赋值了一个匿名函数。注意：这个匿名函数也是一个闭包
	add = ()=>{
		num += 1;
	}
	function f2(){
		console.log(num)
	}
	return f2
}

let result = f1();
result(); // 1
add();
result(); // 2
```

在这段代码中，result实际上就是闭包f2函数。它一共运行了两次，第一次的值是1，第二次的值是2。这证明了，函数f1中的局部变量`num`一直保存在内存中，并没有在f1调用后被自动清除。 

为什么会这样呢？原因就在于f1是f2的父函数，而f2被赋给了一个全局变量，这导致f2始终在内存中，而f2的存在依赖于f1，因此f1也始终在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。 

```js
// 函数中，变量的查找是在函数定义的地方，向上级作用域查找,不是在函数执行的地方
function print(fn){
	const a = 200;
	fn();
}

const a = 100;
function fn(){
	console.log(a);
}

print(fn); // 100
```

### 其它闭包事例

```js
// 利用闭包隐藏数据，只提供Api（这是把函数当对象使用，函数内部的变量，成了私有属性）
function creatCache() {
	const data = {
		name: '张三',
		age: 18
	}
	// 返回一个对面，包含两个函数 set 和 get
	return {
		get(key){
			return data[key]
		},
		set(key, value){
			data[key] = value;
		}
	}
}

// 在函数外部无法直接读取 data
let dataCtr = creatCache();
// 修改 data 的数据
dataCtr.set('name', '盾勇');
// 读取 data 的数据
console.log(dataCtr.get('name'));

```



### 闭包的注意点

1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。

2）闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。



## 9. this

`this`的指向不是在函数定义的时候决定的，而是在函数执行的时候决定的。

```js
// 需要注意一个地方
let obj = {
	name: '张三',
	sayHello(){
		console.log(this);
	},
	wait(){
		// obj.wait()调用时，这里的this是 obj， 下面的this 是 window
		console.log(this)
		setTimeout(function (){
			// 这里的 this 是window， 如果希望这里的this指向obj，可以改成箭头函数的写法
			console.log(this)
		}, 1000)
	}
}


// 为什么上面function里的this指向window？
// 上面的function就相当于是个callback函数，在满足一定条件后被执行
function setTimeOut(callback, delay){
	// 调用callback时，它前面没有和任何对象绑定在一起，根据js规则，默认绑定window
	callback()
}
```



## 10. call、apply和bind

- call、apply 和 bind 都可以改变函数内部 this 的指向

- call、apply 是立即调用，而 bind 是返回对应函数

- 三者在传递参数上也存在区别

    ```js
    let obj = {
        name: 'obj',
        logName(str){
            console.log(this.name, str)
        }
    }
    
    let obj1 = {name: 'java'};
    let obj2 = {name: 'javaScript'}
    
    
    // call 和 apply的区别在传参上
    obj.logName('obj');							// obj obj
    obj.logName.call(obj1, 'call');				// java call
    obj.logName.apply(obj2, ['apply']);			// javaScript apply
    obj.logName.bind(obj1)('bind')				// java bind
    
    // bind可以接受多个参数，除第一个参数外，其余参数将会是返回函数的预置实参
    function fn(...args){
        console.log(args);
    }
    
    const result = fn.bind({name: '张三'}, 'a', 'b', 'c');
    result('d', 'e', 'f'); // ["a", "b", "c", "d", "e", "f"]
    
    ```




### 手写 call 方法

```js
function fn(a, b, c){
    console.log(this);
    console.log(a + b + c);
}

Function.prototype.myCall = function (context, ...args){
    // myCall第一个形参就是最终的this,如果不存在，抛出错误
    if(typeof context !== 'object' || context == null){
        throw new Error('未设置this或this类型错误')
    }
    // 在myCall函数中，this指向它的调用者 fn, 给context添加fn属性，值为fn，
    // 最终context.fn()调用fn函数，此时fn中的this指向context
    context.fn = this;
    // 执行fn，并返回执行结果
    let result = context.fn(...args);
    // 记得清除fn属性，不然fn中的this会多了一个fn属性
    delete context.fn;
    return result;
}

fn.myCall({name: 'hello'}, 1, 2, 3)
```



### 手写 apply 方法

```js
// 和上面基本一样
Function.prototype.myApply = function (context, args = []){
    if(typeof context !== 'object' || context == null){
        throw new Error('未设置this或this类型错误')
    }
    context.fn = this;
    let result = context.fn(...args);
    delete context.fn;
    return result;
}
```



### 手写bind方法

```js
function fn(...args){
    console.log(this);
    console.log(args);
}

Function.prototype.myBind = function (context, ...args){
    if(typeof context !== 'object' || context == null){
        throw new Error('未设置this或this类型错误')
    }
    const fn = this;
    const newFn = function (...rest){
        return fn.apply(context, [...args, ...rest])
    }
    // 处理原型指向问题
    if(fn.prototype){
        newFn.prototype = fn.prototype
    }
    return newFn;
}

let result = fn.myBind({name: 'tom'}, 'a', 'b', 'c');
result('d', 'e', 'f');
```



## 11.异步和单线程

-   `JavaScript`是单线程语言，只能同时做一件事
-   浏览器和 `nodejs`已支持`js`启动**进程**，如 `web worker`
-   `js` 和 `DOM`渲染共用一个进程，因为 `js`可以修改 `DOM`结构
-   遇到需要等待（网络请求、定时任务）的情况，我们不希望进程被卡住，无法做其它事情，所以需要异步
-   异步一般以回调函数 `callback` 的形式来完成



### 同步和异步的区别

同步会阻塞代码的执行，异步不会阻塞代码的执行



### 什么是回调函数，它有什么缺点

作为函数的参数，并在满足一定条件后自动执行的函数称为回调函数。

缺点：

-   某个异步操作需要等待之前的异步操作完成， 这会导致代码的层层嵌套，出现回调地狱
-   某个异步操作要等待多个异步操作的结果，对这种联系的处理，会让代码的复杂度剧增 

`Promise`的出现解决了上面的问题

**异步问题的最终解决方案是 `async await`**



## 12. event loop（事件循环 / 事件轮询）

我们知道JavaScript是单线程运行的，异步是基于回调函数实现。而 event loop 就是异步回调的实现原理。

### 先说一下JavaScript是如何执行的

-   从前到后，一行一行执行
-   如果某一行执行报错，则停止下面代码的执行
-   先把同步代码执行完，再执行异步代码

### event loop 的工作流程

执行栈只执行同步任务，遇到异步代码或者 DOM事件，会将其记录起来，当异步代码 有了结果后（请求获得结果、定时器时间到了）/ DOM事件被触发 ，才会将异步任务的回调函数添加到任务队列中。

当执行栈为空时，先去尝试进行 DOM 渲染。再开启 event loop。event loop会不断查询任务队列中是否有任务。当任务队列不为空时，停止查询，然后将任务移到执行栈中执行。当执行栈再次为空时，event loop又会开始不断查询任务队列的操作。

![](.\images\event-loop.png)

```js
// 以下面代码为例
console.log('Hello');
setTimeout(()=>{
	console.log('异步代码')
}, 1000);
console.log('world');

1.将 console.log('Hello'); 放入执行栈中执行，执行结束后被移除
2.遇到 异步代码 ，不直接在执行栈中执行，记录起来
3.将 console.log('world'); 放入执行栈中执行，执行结束后被移除
4.执行栈中无任务，尝试进行 DOM 渲染。启动 event loop 查询 任务队列中是否有可执行任务（只要执行栈中无任务都会一直查询）
5.过了1s后，异步代码执行结束，setTimeOut里的回调函数被放入到 任务队列 中，event loop 查询到任务队列中有可执行任务，将它放入到执行栈中执行，执行栈为空后，event loop再次进入轮询状态。
```

### event loop 与 DOM渲染

-   每次执行栈被清空（同步代码执行结束）
-   如果存在微任务，执行微任务
-   每次执行栈被清空都是DOM重新渲染的机会。DOM结构如有改变则重新渲染
-   然后再去触发下一次 event loop



## 13. async await 和 promise 的关系

-   执行 async 函数，返回的是 Promise 对象
-   await 相当于 `Promise.then`
-   `try catch` 可捕获异常，代替了 Promise 的 catch



## 14. for of 遍历异步操作

```js
// 现在有一个需求：按顺序以及一定间隔打印数组中每个值得平方值
let arr = [1, 2, 3];
let mult = (num)=> {
	return new Promise(resolve => {
		setTimeout(()=>{
			resolve(num * num)
		}, 1000)
	})
}

// arr.forEach(async (item)=>{
// 	console.log(await mult(item));
// })
// forEach是同步的，执行结果是 1s后同时打印 1 4 9

(async ()=>{
	for(let item of arr){
		console.log(await mult(item))
	}
})()
// 每隔一秒打印数组对应项的平方

```



## 15.宏任务和微任务

宏任务由浏览器决定，微任务由`ES6`规范决定

宏任务： `setTimeout、setInterval、ajax、DOM事件`

微任务：`Promise Async await`

### 微任务执行时机比宏任务要早

```js
console.log(1);

setTimeout(()=>{
	console.log(2)
}, 1000)

Promise.resolve().then(()=>{
	console.log(3)
})

console.log(4)
// 打印顺序： 1 4 3 2

```

### 为什么微任务执行时机比宏任务要早？

像 `setTimeout`这类web api在返回结果后会被放入到任务队列中。当执行栈空闲时，下一步是执行微任务，之后是尝试渲染DOM结构，最后才是去触发 event loop 查询任务队列中是否有可执行任务。所以在执行宏任务之前，DOM已经渲染了。

Promise 是ES规范，不是w3c标准。不会放到和 web api同一个队列，而是被放置到微任务队列 。

宏任务是在DOM渲染后触发的，例如： `setTimeout`

微任务是在DOM渲染前触发的，例如：`Promise`

```html
<!doctype html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<body>
	<script>
		let p = document.createElement('p');
		p.innerText = 'hello world';
		document.body.append(p);

		// 微任务在DOM渲染前执行
		Promise.resolve().then(()=>{
			alert('微任务执行完毕'); // 此时页面上没有出现p标签
		})

		// 宏任务在DOM渲染后触发
		setTimeout(()=>{
			alert('宏任务执行完毕'); // 此时页面上可以看到p标签
		}, 0)
	</script>
</body>
</html>

```



## 16.`DOM`是哪种基本的数据结构？

树 （DOM树）



## 17.`DOM`的本质是什么？

DOM可以理解为浏览器把拿到的html代码，结构化一个浏览器能识别并且js可操作的一个模型 



## 18. DOM节点的 `attr` 和`property`有何区别？ 

`property`：修改对象属性，不会体现到 `html` 结构中

`attribute`修改 `html`标签的属性，会改变 `html`结构

两者都可能引起DOM的重新渲染



## 19.`DOM`性能优化

-   DOM操作比较消耗性能，避免频繁的DOM操作
-   对DOM查询做缓存,避免重复查询 `let pNode = document.querySelector('#title')`
-   将频繁操作改为一次性操作 （例如循环插入节点，可以生成多个节点后，再一次性插入到父节点中）

```html
<!doctype html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<body>
	<ul id="list">

	</ul>
	<script>
		let ul = document.querySelector('#list');
		// 创建一个文档片段， 此时还没有插入到DOM树中，for循环里的appendChild不会导致DOM重新渲染
		let fragment = document.createDocumentFragment();
		for(let i=0; i<6; i++){
			let liNode = document.createElement('li');
			liNode.innerText = `第${i + 1}个li标签`;
			fragment.appendChild(liNode);
		}
		list.appendChild(fragment);
	</script>
</body>
</html>

```



## 20.写一个通用的事件绑定函数

```js
function bindEvent(element, eventType, callback){
    element.addEventListener(eventType, callback)
}
```



## 21.事件捕获和冒泡

假设现在有一个三层结构，都绑定了点击事件。 我点击 three 的时候，从父节点到子节点，查找到 three 对应的节点，这个过程就是事件捕获。

我点击了three节点，依次触发了 three、 two、 one节点的点击事件，这就叫事件冒泡

```html
<div id="one">
    one
    <div id="two">
        two
        <div id="three">three</div>
    </div>
</div>
```

### 阻止事件冒泡

```js
// w3c
e.stopPropagation();
// IE
e.cancelBubble = true;
```



## 22. 事件代理（委托）

当我们需要给很多同级节点添加相同的事件时，我们可以将事件绑定在它们共同的父节点上，通过事件冒泡触发父节点上的事件，这就叫事件代理。



## 23. 手写一个简易的`AJax`

`xhr.readyState`

-   0：未初始化，此时未调用 `send()`
-   1：载入，已调用 `send()`，正在发送请求
-   2：载入完成，`send()`执行完成，已经接受到全部响应内容
-   3：交互，正在解析响应内容
-   4：完成，响应内容解析完成，可以在客户端调用

`xhr.status`

-   2xx：表示成功处理请求，例如 200
-   3xx：需要重定向，浏览器直接跳转。301（永久重定向），302（临时重定向），304（请求的资源和上次请求获得的资源一样，没有发生改变，直接使用缓存）
-   4xx：客户端请求错误。404，403（没有权限）
-   5xx：服务端错误

```js
// get
const xhr = new XMLHttpRequest();
xhr.open('get', './data.json');
xhr.send();
xhr.onreadystatechange = function (){
    if(xhr.readyState === 4 && xhr.status === 200){
        console.log(JSON.parse(xhr.response))
    }
}
```

```js
// post
const xhr = new XMLHttpRequest();
xhr.open('post', './data.json');
const params = {
    userId: 12346,
    time: Date.now()
};
xhr.setRequestHeader('Content-Type', 'application/json')
xhr.send(JSON.stringify(params));
xhr.onreadystatechange = function (){
    if(xhr.readyState === 4 && xhr.status === 200){
        console.log(JSON.parse(xhr.response))
    }
}
```



## 24.跨域

### 为什么会出现跨域（同源策略）

-   `ajax`请求时，浏览器要求当前网页端和服务器端必须同源
-   同源：协议（`http、https`）、域名（`www.a.com、www.b.com`）、端口（默认是80），三者必须一致

### 加载 图片、`Css`、`JS`可无视同源策略

-   `<img src="跨域图片地址" alt="">`
-   `<link rel="stylesheet" href="跨域css地址">`
-   `<script src="跨域js地址"></script>`，可以利用这个特性实现 `jsonp`
-   所有的跨域，都必须经过服务端的允许和配合

### `JSONP`

利用 `<sctipt>`可以绕过跨域、服务端可以任意动态拼接数据返回的特性，我们可以使用 `<script>`来获得跨域数据

```html
<script>
    // 这个函数用于处理请求数据的
    function callback(res){
        console.log(res)
    }
</script>
<!--
这里模拟一个跨域请求，这个请求会返回js
返回的js内容为 callback({code: 200, data: {name: '张三', age: 18}})
-->
<script src="http://localhost:3036/getUserInfo?callback=callback"></script>
```

```js
// server端代码
const express = require('express');
const app = express();

app.get('/getUserInfo', (req, res) => {
	const {callback} = req.query;
	let data = {
		code: 200,
		data: {
			username: '张三',
			age: 18,
			phone: 18312345678,
			address: '广州市天河区万科云广场'
		}
	}
	res.send(`${callback}(${JSON.stringify(data)})`)
})

app.listen(3036, ()=>{
	console.log('server running in 3036')
})

```



## 25. cookie、`localStorage`、`sessionStorage`的区别

### cookie

-   本身用于浏览器和服务端通讯
-   在没有`localStorage`、`sessionStorage`的时代，被借用来做存储
-   可用 `document.cookie = ''` 来修改cookie

##### 缺点：存储空间最大为`4kb`，`http`请求会携带cookie，增加了请求数据量

```js
document.cookie = 'a=100;b=200';
console.log(document.cookie); // a=100, cookie以;分割，每次设置时只会截取第一个
document.cookie = 'b=200';
console.log(document.cookie); // a=100;b=200;
// 修改已存在的key
document.cookie = 'a=101';
console.log(document.cookie); // b=200;a=101
```



### `localStorage` 和 `sessionStorage`

-   `Html5` 专门为存储而设计的，最大存储空间为 `5M`
-   不会随着 http 请求被发送出去
-   `localStorage`的数据会永久存储，除非我们手动删除
-   `sessionStorage`的数据只存在于当前会话，浏览器被关闭，则数据被清空



## 26.从输入 `url` 到渲染出页面的整个过程



## 27.性能优化方案



## 28.防抖与节流

### 函数防抖（`debounce`）

函数防抖：一个需要频繁触发的函数，在规定时间内，只让最后一次生效，前面的不生效。

```js
// 实现原理：
// 第一次执行函数时，创建一个定时器，在指定的时间间隔后执行对应的逻辑
// 下一次执行函数时，如果存在定时器，清除上一次的定时器，再创建一个新的定时器

function debounce(fn, delay) {
    let timer = null;
    // 用到了闭包的特性，可以使变量timer的值长期保存在内存中。
    return ()=>{
        timer && clearTimeout(timer);
        timer = setTimeout(()=>{
            fn();
        }, delay)
    }
}

// 使用
const fn = debounce(()=>{
    console.log('fn')
}, 1000);

fn();
fn();
```



### 函数节流（throttle）

函数节流：函数在执行一次后，只有在大于设定的执行周期之后才会执行第二次

```js
// 实现原理：
/*
用 时间戳 来判断是否已到回调该执行时间，记录上次执行的时间戳，然后每次触发 scroll 事件执行回调，回调中判断当前时间戳距离上次执行时间戳的间隔是否已经到达 规定时间段，如果是，则执行，并更新上次执行的时间戳，如此循环；
*/
function throttle(fn, delay) {
    // 记录上一次函数触发的时间
    let lastTime = 0;
    return ()=>{
        let nowTime = Date.now();
        if(nowTime - lastTime > delay){
            fn.call(this);
            lastTime = nowTime;
        }
    }
}
```



## 29. 如何预防 `xss` 攻击 

`xss`攻击，中文名为跨站脚本攻击，利用 `script img`这类可以跨域的标签来执行第三方脚本

### `xss`攻击场景

前端界面存在留言板，攻击者将 `<script src='...'></script>` 这类文本输入进去，并由前端上传到了服务端。

在留言板显示界面就会执行这个脚本。

```html
留言板输入
<script>alert(“hey!you are attacked”)</script>

留言板显示界面的html
<html>
    <head>
       <title>留言板</title>
    </head>
<body>
    <div id=”board” 
            <script>alert(“hey!you are attacked”)</script>
    </div>     
</body>
</html>
```

### `xss`的危害

-   窃取用户 cookie 等用户凭证，实现无密码自动登录
-   恶意跳转到第三方网站

### 预防：

将不可信的文本内容装换为`html`实体编码





## 30. 如何预防 `xsrf` 攻击 

### 什么是`CSRF`

跨站请求攻击 ，是一种挟制用户在当前已登录的Web应用程序上执行非本意的操作的攻击方法。  

简单地说，是攻击者通过一些技术手段欺骗用户的浏览器去访问一个自己曾经认证过的网站并执行一些操作（如发邮件，发消息，甚至财产操作如转账和购买商品）。由于浏览器曾经认证过，所以被访问的网站会认为是真正的用户操作而去执行。这利用了web中用户身份验证的一个漏洞：**简单的身份验证只能保证请求发自某个用户的浏览器，却不能保证请求本身是用户自愿发出的**。

```js
例子：假如一家银行用以执行转账操作的URL地址如下： 
// http://www.examplebank.com/withdraw?account=AccoutName&amount=1000&for=PayeeName
那么，一个恶意攻击者可以在另一个网站上放置如下代码： 
// <img src="http://www.examplebank.com/withdraw?account=Alice&amount=1000&for=Badman">

如果有账户名为Alice的用户访问了恶意站点，而她之前刚访问过银行不久，登录信息尚未过期，那么她就会损失1000资金。
```



### 预防

-   添加校验 `token`， `token`在客户端是存放在cookie中，当用户去访问这个恶意网站的时候，是没办法拿到我们银行转账网站的cookie的。
-   网站操作添加验证码校验

