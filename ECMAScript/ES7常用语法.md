## 1. includes （数组方法）

 Includes 方法用来检测数组中是否包含某个元素，返回布尔类型值 

```js
// es5判断数组是否存在某个元素用 indexOf('') !== -1

// es7 可以用includes
let singerList = ['周杰伦', '陈奕迅', '林俊杰']
console.log(singerList.includes('周杰伦')) // true

// 字符串也有includes方法,用来查询是否包含指定的字符串
const str = 'hello world & 123';
console.log(str.includes('hello')); // true

```



## 2. 指数操作符 (**)

 在 ES7 中引入指数运算符「**」，用来实现幂运算，功能与 `Math.pow` 结果相同 

```js
// 求3的6次方
console.log(3 ** 6)
console.log(Math.pow(3, 6))
```

