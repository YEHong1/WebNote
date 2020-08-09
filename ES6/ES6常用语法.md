## 1.let、const

-   let 关键词声明的变量不具备变量提升（hoisting）特性
-   let 和 const 声明只在最靠近的一个块中（花括号内）有效
-   当使用常量 const 声明时，请使用大写变量，如：CAPITAL_CASING
-   const 在声明时必须被赋值



## 2.箭头函数

```js
// 箭头函数的基本写法
let func = num => num;
let func = () => num;
let sum = (num1,num2) => num1 + num2;
[1,2,3].map(x => x * x);

// 箭头函数基本特点

// 1.箭头函数this为父作用域的this，不是调用时的this
document.body.onclick = () => {
    // 此时，this指向window，而不是document.body这个DOM对象
    console.log(this);
}

// 2.箭头函数没有arguments，caller，callee
// 3.箭头函数通过call和apply调用，不会改变this指向，只会传入参数
// 4.箭头函数没有原型属性
// 5.箭头函数返回对象时，要加一个小括号
let func = () => ({ foo: 1 });


```



## 3.形参默认值

```js
function add(a = 1, b = 2) {
    return a + b
}

console.log(add()); // 3
```



## 4.对象简写

```js
let make = 'one';
let num = 1;

// 同名属性简写、
let obj1 = {
    make,
    num
}
//相当于
let obj1 = {
    make: make,
    num: num
}


// 对象方法简写
let obj2 = {
    add(x, y) {
        return x + y
    }
}
//相当于
let obj2 = {
    add: function(x, y) {
        return x + y
    }
}
```



## 5.对象和数组解构

```js
// 数组解构
let arr = [1, 2, 3];
let [a, b, c] = arr;
console.log(a, b, c); // 1, 2, 3

// 对象解构
let obj = {name: '张三', age: 18};
let {name, age} = obj;
console.log(name, age); // '张三', 18
```



## 6.模板字符串

```js
let str = '=';
console.log(`1 + 2 ${str} 3`); // 1 + 2 = 3
```



## 7.Set

```js
// Set时ES6提供的新数据结构，类似数组，但它的一大特性就是所有元素都是唯一的，没有重复。
// 常用来做数组的去重
let set = new Set([1, 1, 2, 3, 3, 3]);
console.log(set); // [1, 2, 3]

// 添加一个元素
set.add(1); // 数据中已存在的话不会添加进去

// 删除一个元素
set.delete(1);

// 判断某元素是否存在
set.has(1); // 返回true 或 false

// set类型转换为数组
let arr = [...set]

```



## 8.扩展运算符

```js
// 对象使用
let obj1 = {name: '123', age: 18};
// 浅拷贝对象
let obj2 = {...obj1};
// 等价于
let obj2 = Object.assign({}, obj1);


// 数组使用
let arr = [1, 2, ,3];
let [a, ...b] = arr;
console.log(a, b); // 1, [2, 3]

// 合并数组
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];
let arr3 = [...arr1, ...arr2];
console.log(arr3); // [1, 2, 3, 4, 5, 6]

let arr = [1, 2, 3];
console.log(...arr); // 1, 2, 3

```

















