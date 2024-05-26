## 1.let声明变量

```js
1.不能重复声明
let name = 'admin';
let name = 'root';
// 上面代码重复声明了name。
// 浏览器提示 Uncaught SyntaxError: Identifier 'name' has already been declared

2.存在块级作用域
if(Date.now()){
    let girl = '小红';
}
console.log(girl);
// 提示 girl未定义 Uncaught ReferenceError: girl is not defined

3.不存在变量提升
console.log(count);
let count = 0;
// 无法在初始化之前访问'count'
// Uncaught ReferenceError: Cannot access 'count' before initialization
// 如果上面的count使用var定义则会打印 undefined

4.不影响作用域链
// 作用域是在运行时代码中的某些特定部分中变量，函数和对象的可访问性。
// 换句话说，作用域决定了代码区块中变量和其他资源的可见性。
// 在当前作用域查找不到变量时，向上一级作用域继续寻找，这就是作用域链
let count = 0;
function add(){
    // add函数作用域中不存在count变量，向上一级查询
    count += 1;
}
add(); // 执行 add 函数后 count 值为1
```

```js
// 使用var定义变量时存在的一些问题
for(var i=0; i<2; i++){
    setTimeout(()=>{
        console.log(i)
    }, 0)
}
// 上面的代码执行后会打印2次 2，这是因为var定义的变量是存在变量提升的，且console.log是异步执行的
// 上面的代码和下面是一样的
var i;
for(i=0; i<2; i++){
    setTimeout(()=>{
        console.log(i)
    }, 0)
}
// 使用let定义的话，则可以打印 0, 1
```



## 2.const定义变量

```js
1.一定要赋初始值
const A = 'A';

2.一般常量使用大写

3.常量的值不能修改

4.存在块级作用域

5.不存在变量提升

6.对对象、数组中的元素进行修改，不算做对常量的修改，不会报错
```



## 3.解构赋值

 ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称 为解构赋值。 

```js
1.对象的解构赋值
let student = { name: '小明', age: 18, studentId: '006', sex: '男'};
// 获取student的name和studentId属性
let { name, studentId } = student;
console.log(name, studentId); // 小明 006

2.数组的解构赋值
// 数组的结构赋值需要按顺序
let singerArr = ['周杰伦', '陈奕迅', '林俊杰'];
const [ZJL, CYX] = singerArr;
console.log(ZJL, CYX); // 周杰伦 陈奕迅
```



## 4.模板字符串

 模板字符串（template string）是增强版的字符串，用反引号（`）标识，特点 ：

-    字符串中可以出现换行符 
-    可以使用 ${xxx} 形式输出变量 

```js
// 定义字符串
let str = `<ul>
    <li>沈腾</li>
    <li>玛丽</li>
    <li>魏翔</li>
    <li>艾伦</li>
</ul>`;
// 变量拼接
let star = '王宁';
let result = `${star}, 男, 18岁， 热情开朗`;
```



## 5.简化对象写法

 ES6 允许在大括号里面，直接写入变量和函数，作为对象的属性和方法。这 样的书写更加简洁。  

```js
let name = '张三';
let age = 18;

// 1.普通写法
let onj1 = {
    name: name,
    age: age,
    sayHello: function () {
        console.log('hello')
    }
}

// 2.对象简写
let obj2 = {
    // 同名属性简写
    name,
    age,
    // 省略 : function
    sayHello(){
        console.log('hello')
    }
}
```



## 6.箭头函数

```js
// es6允许使用 => 来定义函数
let fn1 = function () {
    console.log('fn1')
}

let fn2 = ()=>{
    console.log('fn2')
}

// 特点:
1.箭头函数本身没有this, 它的this始终指向函数声明时所在作用域下的this的值
window.name = 'Hello World';
const lucy = { name: 'lucy' };
let fn = ()=>{
    console.log(this.name);
}
// 使用call, apply, bind并不会改变箭头函数中的this指向。
// 代码执行后打印 Hello World 而不是 lucy
fn.call(lucy);

2.不能作为构造函数来实例化对象
let Person = (name, age)=>{
    this.name = name;
    this.age = age;
}

let personObj = new Person('张三', 18)
// Uncaught TypeError: Person is not a constructor

3.箭头函数内不能使用arguments变量
// arguments 是一个对应于传递给函数的参数的类数组对象
let fn1 = function () {
    console.log(arguments)
}
fn1(1, 2, 3);
// [Arguments] { '0': 1, '1': 2, '2': 3 }

let fn2 = ()=>{
    console.log(arguments)
}
fn2(1, 2, 3);
// Uncaught ReferenceError: arguments is not defined

// 箭头函数没有原型对象 prototype 这个属性
```

```js
// 箭头函数的一些简写情况

// 1.当函数有且只有一个参数时，可以省略小括号
let fn1 = a => {};

// 2.当函数体只有一条语句时可以省略花括号 {}, 此时函数体的执行结果为函数返回值(省略花括号时不能写return)
let fn2 = n => n * n;
fn2(6); // 函数返回值为36
```



## 7.函数参数默认值

在定义函数时给参数设置默认值，当不传参数调用该函数时，参数使用默认值。

```js
// 示例
let fn = (name, age = 18)=>{
    console.log(name, age)
}
fn('张三'); // 张三 18
fn('小明', 20); // 小明 20

// 也可以结合解构赋值来用
let state = { name: '小明'}
const {
    name = '张三',
    age = 18
} = state;
console.log(name, age); // 小明 18
```



## 8. rest参数

 ES6 引入 rest 参数，用于获取函数的实参，用来代替 arguments 

```js
// 示例
let fn = (...args)=>{
	console.log(args);
}

fn(1, 2, 3); // [1, 2, 3] 是一个数组， arguments是类数组对象

// 注意：rest参数必须要放在参数最后面
let fn1 = (a, b, ...args) => {
    console.log(a);
    console.log(b);
    console.log(args);
}

fn1(1, 2, 3, 4, 5, 6); 
// 打印 
// 1
// 2
// [3, 4, 5, 6]
```



## 9.扩展运算符

 扩展运算符（spread）是三个点（`...`）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。 

```js
let singerList = ['周杰伦', '陈奕迅', '林俊杰'];

function getSingerName() {
	console.log(arguments)
}

// 相当于 getSingerName('周杰伦', '陈奕迅', '林俊杰');
getSingerName(...singerList);

// 扩展运算符也可以用于对象
let obj = {name: '张三', age: 18}
let newObj = {...obj, sex: '男'}
// {name: '张三', age: 18, sex: '男'}
```



## 10. Symbol

ES6 引入了一种新的原始数据类型 Symbol，表示独一无二的值。它是 JavaScript 语言的第七种数据类型，是一种类似于字符串的数据类型。 

```js
// 特点
1.Symbol的值是唯一的，用来解决命名冲突的问题
2.Symbol的值不能与其它数据进行运算（比较大小也不可以）
3.Symbol定义的对象属性不能使用for…in循环遍历，但是可以使用Reflect.ownKeys来获取对象的所有键名

// 定义Symbol有两种方式: Symbol('参数') 和 Symbol.for('参数')
// Symbol.for() 传入的值相同，则这两个变量相等
let symbol1 = Symbol('周杰伦');
let symbol2 = Symbol('周杰伦');
console.log(symbol1, symbol1 === symbol2); // Symbol(周杰伦) false
let symbol3 = Symbol.for('陈奕迅');
let symbol4 = Symbol.for('陈奕迅');
console.log(symbol3 === symbol4); // true

```

```js
// Symbol的应用场景
ES5 的对象属性名都是字符串，这容易造成属性名的冲突。比如，你使用了一个他人提供的对象，但又想为这个对象添加新的方法（mixin 模式），新方法的名字就有可能与现有方法产生冲突。如果有一种机制，保证每个属性的名字都是独一无二的就好了，这样就从根本上防止属性名的冲突。这就是 ES6 引入Symbol的原因。

// 假设我有一个game对象，里面有很多属性，我想要加一个方法，但是却担心新增加的方法和已有的方法重名，导致覆盖问题
let game = {}
let up = Symbol('up');
game[up] = ()=>{
	console.log('我是新增的up方法')
}
game[up]();
```



## 11.迭代器

遍历器（Iterator）就是一种机制。它是一种接口，为各种不同的数据结构提 供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作。 

```js
1.ES6 创造了一种新的遍历命令 for...of 循环，Iterator 接口主要供 for...of 消费 

2.原生具备 iterator 接口的数据(可用 for of 遍历)
  Array、Arguments、Set、Map、String、TypedArray、NodeList
  
3.工作原理
  创建一个指针对象，指向当前数据结构的起始位置
  第一次调用对象的 next 方法，指针自动指向数据结构的第一个成员
  接下来不断调用 next 方法，指针一直往后移动，直到指向最后一个成员
  每调用 next 方法返回一个包含 value 和 done 属性的对象

  
let singerList = ['周杰伦', '陈奕迅', '林俊杰'];

for(let singer of singerList){
	console.log(singer)
}
// 周杰伦
// 陈奕迅
// 林俊杰
// 如果使用的是for in的话会打印数组下标，for in 对应 key， for of 对应value

```

```js
// Es5自定义迭代器
function createIterator(items) {
    var i = 0;
    return {
        next: function() {
            var done = (i >= items.length);
            var value = !done ? items[i++] : undefined;
            return {
                done: done,
                value: value
            };
        }
    };
}
var iterator = createIterator([1, 2, 3]);
console.log(iterator.next()); // "{ value: 1, done: false }"
console.log(iterator.next()); // "{ value: 2, done: false }"
console.log(iterator.next()); // "{ value: 3, done: false }"
console.log(iterator.next()); // "{ value: undefined, done: true }"
// 之后的所有调用
console.log(iterator.next()); // "{ value: undefined, done: true }"
```



## 12.生成器函数

 生成器函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。

```js
// 定义生成器函数
function * gen(){
	console.log('111');
	yield '语句1返回的结果';
	console.log('222');
	yield '语句2返回的结果';
	console.log('333');
	yield '语句3返回的结果';
}

// 直接调用gen函数不执行函数体代码，而是返回一个迭代器对象
let Iterator = gen();
let value1 = Iterator.next(); // 打印 111
console.log(value1); //{ value: '语句1返回的结果', done: false }
let value2 = Iterator.next(); // 打印 222
console.log(value2); //{ value: '语句2返回的结果', done: false }
let value3 = Iterator.next(); // 打印 333
console.log(value3); //{ value: '语句3返回的结果', done: false }


1.生成器函数的定义需要在function 和 函数名中间加一个 *
2.生成器函数返回的结果是迭代器对象，调用迭代器对象的 next 方法可以得到 yield 语句后的值
3.yield 相当于函数的暂停标记，也可以认为是函数的分隔符，每调用一次 next 方法，执行一段代码
```

```js
4.next 方法可以传递实参，作为 yield 语句的返回值
  下一次调用的next接受的实参将作为上一次 yield 的返回值

function * gen(args){
	console.log(args);
	let one = yield '1';
	console.log(one);
	let two = yield '2';
	console.log(two);
	let three = yield '3';
	console.log(three);
}

let Iterator = gen('666'); // 执行到这里时，没有任何打印
let value1 = Iterator.next('???'); // 打印666
console.log(value1) // { value: '1', done: false }
let value2 = Iterator.next('第2次调用next传递的值'); // 打印第2次next传递的值
console.log(value2) // { value: '2', done: false }
let value3 = Iterator.next('第3次调用next传递的值'); // 打印第3次next传递的值
console.log(value3) // { value: '3', done: false }
let value4 = Iterator.next('第4次调用next传递的值'); // 打印第3次next传递的值
console.log(value4) // { value: undefined', done: true }

```

```js
// 使用生成器函数解决异步问题示例
// 需求 1s后控制台打印 1, 2s后控制台打印 2, 3s后控制台打印 3
function one(){
	setTimeout(()=>{
        console.log(1);
		Iterator.next();
	}, 1000)
}

function two(){
	setTimeout(()=>{
		console.log(2);
		Iterator.next();
	}, 2000)
}

function three(){
	setTimeout(()=>{
		console.log(3);
		Iterator.next();
	}, 3000)
}

function * gen() {
	yield one();
	yield two();
	yield three();
}

let Iterator = gen();
Iterator.next();
```



## 13.Promise（有专门的笔记，这里不做记录）



## 14.Set

ES6 提供了新的数据结构 Set（集合）。它类似于数组，但成员的值都是唯 一的，集合实现了 iterator 接口，所以可以使用『扩展运算符』和『for…of…』进 行遍历，集合的属性和方法 

```js
// 声明，可以传入可迭代数据，一般传入一个数组
let s = new Set(['周杰伦', '陈奕迅', '林俊杰', '周杰伦', '陈奕迅', '林俊杰'])
console.log(s) // Set(3) { '周杰伦', '陈奕迅', '林俊杰' } 对象类型
// 查看元素个数
console.log(s.size)
// 添加新元素
s.add('张学友');
// 删除一个元素
s.delete('张学友')
// 检查是否含有元素, 存在返回true， 不存在返回false
console.log(s.has('张学友'))
// 清空
s.clear()
```

```js
// Set的应用
1.数组去重
let arr1 = [1, 1, 2, 2 , 3, 3]
// new Set得到的是一个不包函重复元素的对象，需要转为数组
let result1 = [...new Set(arr1)];
console.log(result1)

2.获取两个数组的交集w
let arr2 = [3, 6, 9]
let result2 = [...new Set(arr1)].filter(item=> new Set(arr2).has(item))
console.log(result2)

3.并集
let result3 = [...new Set([...arr1, ...arr2])]
console.log(result3)
```



## 15. Map

ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合。但是“键” 的范围不限于字符串，各种类型的值（包括对象）都可以当作键。Map 也实现了 iterator 接口，所以可以使用『扩展运算符』和『for…of…』进行遍历。 

```js
// Map像是一个升级版的对象，可以使用非字符串key

// 声明Map
let m = new Map();
// 添加元素，用set
m.set('name', '张三');
let objKey = {
	game: 'LOL'
}
// Map的key可以是对象类型
m.set(objKey, '对象类型的key对应的value')

// size查看属性个数
console.log(m) // Map(2) { 'name' => '张三', { game: 'LOL' } => '对象类型的key对应的value' }
console.log(m.size) // 2

// delete 删除属性
m.delete(objKey);
console.log(m) // Map(1) { 'name' => '张三' }

// get 获取属性
console.log(m.get('name')) // 张三

```



## 16. Class

ES6 提供了更接近传统语言的写法，引入了 Class（类）这个概念，作为对 象的模板。通过 class 关键字，可以定义类。基本上，ES6 的 class 可以看作只是 一个语法糖，它的绝大部分功能，ES5 都可以做到，新的 class 写法只是让对象 原型的写法更加清晰、更像面向对象编程的语法而已。 

```js
// es5 构造函数
function Phone(brand, price) {
	this.brand = brand;
	this.price = price;
}

Phone.prototype.call = ()=>{
	console.log('打电话')
}

let MI = new Phone('小米', 2999)
MI.call();
```

```js
// es6 Class写法
class Phone {
	// constructor在 new Person时被执行
	constructor(brank, price) {
		this.brank = brank;
		this.price = price;
	}

	// 方法必须使用该语句，不能使用es5对象的完整写法: call: function(){}
	call(){
		console.log(`用${this.brank}手机打电话`)
	}
}

let HUAWEI = new Phone('华为', 5999)
HUAWEI.call();
```



### 静态成员

```js
1.es5
function Phone() {}
Phone.name = '手机';
// 想要实例能够获取到name的值可以使用原型
// Phone.prototype.name = '手机'
Phone.change = ()=>{
	console.log('我可以改变世界')
}

let mi = new Phone('小米', 1999);
// mi.name的值为undefined，
// 因为定义的是Phone这个函数对象的属性，Phone的实例无法获取到name的值，这种在class中称为静态属性
console.log(mi.name);

2.es6
class NewPhone{
	static name = '手机';
	static change = ()=>{
		console.log('我可以改变世界')
	}
}

console.log(NewPhone.name); // 手机
let oneNote = new NewPhone();
console.log(oneNote.name) // undefined



```



### 继承

```js
// es5
function Phone(brank, price) {
	this.brank = brank;
	this.price = price;
}

Phone.prototype.call = function (){
	console.log(`用${this.brank}手机打电话`)
}

// 继承
function SmartPhone(brank, price, color, size) {
	Phone.call(this, brank, price);
	this.color = color;
	this.size = size;
}

// 设置子构造函数的原型
SmartPhone.prototype = new Phone;
SmartPhone.prototype.constructor = SmartPhone;

// 声明子类方法
SmartPhone.prototype.photo = function (){
	console.log('我可以拍照')
}

let mi = new SmartPhone('小米', 1999, '蓝色', 6.1);
mi.call();
mi.photo();

```

```js
// es6
class Phone{
	constructor(brank, price) {
		this.brank = brank;
		this.price = price;
	}

	call(){
		console.log(`用${this.brank}手机打电话`)
	}
}

// extends 继承
class SmartPhone extends Phone{
	constructor(brank, price, color, size) {
		// 调用super即调用父类的constructor
		super(brank, price);
		this.color = color;
		this.size = size;
	}

	photo(){
		console.log('拍照片')
	}
}

let huaWei = new SmartPhone('华为', 5999, '蓝色', 5.6)
huaWei.call();
huaWei.photo();

```



### 子类对父类方法的重写

只需要在子类中添加一个和父类方法同名的方法即可

```js
// 假设Phone类中已经定义call方法
class SmartPhone extends Phone{
	constructor(brank, price, color, size) {
		super(brank, price);
		this.color = color;
		this.size = size;
	}

	photo(){
		console.log('拍照片')
	}
    
    // 在子类中也定义一个call方法
    call(){
        console.log('SmartPhone的call方法')
    }
}

let huaWei = new SmartPhone('华为', 5999, '蓝色', 5.6)
// 这里调用call是子类的call方法，而不再是父类的call方法
huaWei.call();
huaWei.photo();
```



### class中的get和set

```js
class Phone{
	get price(){
		console.log('查询手机价格')
		// 返回值作为 phone.price的值
		return 1999
	}

	set price(newValue){
		console.log('手机价格被修改为' + newValue)
	}
}

let mi = new Phone();
// 触发get
console.log(mi.price);
// 触发set
mi.price = 999

```



## 17. 对象方法扩展

 Object.is 比较两个值是否严格相等，与『===』行为基本一致（在0 与 NaN 的比较上存在差异） 

```js
// Object.is() 判断两个值是否完全相等
console.log(Object.is(100, 100)) // true
console.log(Object.is(100, '100')) // false
// 和 === 的区别在于0、NaN的对比上
console.log(Object.is(NaN, NaN)) // true
console.log(NaN === NaN) // false
console.log(+0 === -0) // true
console.log(Object.is(+0, -0)) // false
```

 Object.assign 对象的合并，将源对象的所有可枚举属性，复制到目标对象 

```js
// Object.assign(obj1, obj2)  合并两个对象的值，存在同名属性时，后面的覆盖前面的
let obj1 = {
	name: '张三',
	age: 18,
	like: ['打游戏']
}

let obj2 = {
	name: '张三丰',
	age: 36,
	professional: '司机'
}

// { name: '张三丰', age: 36, like: [ '打游戏' ], professional: '司机' }
console.log(Object.assign(obj1, obj2));
```



## 18.Es6的模块化

 模块化是指将一个大的程序文件，拆分成许多小的文件，然后将小文件组合起来。 

 模块化的优势有以下几点：  

-    防止命名冲突 
-    代码复用 
-    高维护性 

```js
// es6 模块功能主要由两个命令构成：export 和 import。
export有两种暴露方式：
1.分别暴露
export let school = '北京朝阳小学';
export let sayHello = ()=>{
    console.log('Hello')
}

2.整体暴露
let school = '北京朝阳小学';
let sayHello = ()=>{
    console.log('Hello')
}

export default {
    school,
    sayHello
}

import也有两种导入方式
1.通用导入
import * as 别名 from '文件对应位置'
2.解构赋值型导入
export default {
    school,
    sayHello
}
import {school， sayHello} from '文件对应位置'



// 在html中使用es6模块化写法：
<script type="module">
    // Index是 index.js暴露出来的所有内容的别名
    import * as Index from './index';
</script>

// 一般我们只引入一个js文件作为入口文件，然后在这个js文件中import其它文件
<script src='./index.js' type="module"/>
```

