## 1.1 DOM简介

- 文档对象模型（Document Object Modal），简称DOM。
- 用来处理可扩展标记语言（Html, Xml）的标准编程接口



## 2.1 如何获取DOM元素

- 通过id获取

  ```js
  // 返回一个对象，元素不存在则返回null
  let root1 = document.getElementById('root1');
  let root2 = document.querySelector('#root2');
  // 打印DOM对象推荐用console.dir()
  console.dir(root1, root2);
  ```

- 根据标签名获取

  ```js
  // 返回一个元素集合，是一个伪数组
  let lis1 = document.getElementsByTagName('li');
  let lis2 = document.querySelectorAll('li');
  ```

- 通过类名获取

  ```js
  // 通过类名来获取元素集合，也是返回伪数组, 是H5新增的方法
  let box = document.getElementsByClassName('box');
  ```

  

## 2.2 获取html和body元素

```js
// 获取body、html
let body = document.body;
let html = document.documentElement;
```



## 3.1 事件三要素

- 事件源，一般是指某个元素
- 事件类型，比如点击事件
- 事件处理函数

```js
let btn = document.getElementById('btn');
btn.onclick = function () {
  alert('Hello World')
}
```



## 3.2 注册时间的两种方式

```js
// 1.传统方式
// 以on开头，特点： 注册事件唯一性
// 也就是说同一个元素、同一个事件只能设置一个处理函数，后定义的会覆盖之前的
btn.onclick = function () {
  console.log(1);
};

btn.onclick = function () {
  console.log(2);
};

// 点击btn，只会打印2


// 2.方法监听注册方式
node.addEventListener('事件类型，不加on', function(){}, false);
// 第三个参数默认为false，
// true表示该元素在事件的“捕获阶段”（由外往内传递时）响应事件
// false表示该元素在事件的“冒泡阶段”（由内向外传递时）响应事件
// 特点是同一个元素、同一个事件可以注册多个监听器，按注册顺序依次执行
// 存在兼容性问题，IE低版本不支持 addEventListener
btn.addEventListener('click', function(){console.log(1)});
btn.addEventListener('click', function(){console.log(2)});
// 点击btn， 打印1 2


// 3.IE8，可以绑定多个事件
btn.attachEvent("有on的事件名字",事件处理函数)

```



## 3.3 事件解绑

```js
1
节点.on事件名字=事件处理函数--->绑定事件
节点.on事件名字=null;
2
节点.addEventListener("没有on的事件类型",命名函数,false);---绑定事件
节点.removeEventListener("没有on的事件类型",函数名字,false);
3
节点.attachEvent("on事件类型",命名函数);---绑定事件
节点.detachEvent("on事件类型",函数名字);
```



## 3.4 event.target 和 this 的区别

```js
事件对象e.target 和 this的区别
事件对象指向触发事件的对象， this指向监听事件的对象
// 例如： div注册了一个点击事件，点击div下的p，
// e.target 指向 p
// e.currentTarget 指向绑定事件的对象 div
// this 指向绑定监听事件的对象 div
```



## 3.5 阻止事件冒泡

```js
// w3c
e.stopPropagation();
// IE
e.cancelBubble = true;
```



## 3.6 阻止事件默认行为

```js
// w3c
e.preventDefault();
// IE
e.returnValue = false;
```



## 4.1 修改元素内容

```js
let root = document.getElementById('root');
// innerText 和 innerHTML的区别为 innerHTML可解析html标签
// 推荐使用 innerHTML
// innerText是非标准的，且火狐浏览器不支持
root.innerText = Date.now();
root.innerHTML = `<b>${Date.now()}</b>`;
```



## 4.2 修改元素属性

```js
// 法一，用于元素内置属性，无法获取元素内的自定义属性
element.src = 'src';

// 法二，一般用于自定义属性
element.getAttribute('属性名');
element.setAttribute('属性名', '属性值');
elemnet.removeAttribute('属性名');
```



## 4.3 修改元素样式

```js
// 1.修改少量样式
element.style.color = 'sky';

// 2.修改大量样式时，用过类名来修改
element.className = 'pinkClass';
// 元素有多个类名，可以通过classList来处理
element.classList.add('在元素中添加一个或多个类名');
element.classList.remove('在元素中移除一个或多个类名');
```



## 5.1 获取元素的父节点

```js
let parent = node.parentNode;
```



## 5.2 获取元素的子节点

```js
// childNodes 会获取到所有的子节点，包括 元素节点 和 文本节点
let children1 = parent.childNodes; 
// children 获取所有的子元素节点, 一般都是使用children来获取元素子节点
let children2 = parent.children;
```



## 5.3 子节点的相关api

```js
// 获取第一个子节点，可能是 文本节点 或 元素节点
let firstChild = parent.firstChild;
// 获取最后一个子节点，可能是 文本节点 或 元素节点
let lastChild = parent.lastChild;

// 在开发中获取第一个子元素和最后一个子元素
firstChild = parent[0];
lastChild = parent[parent.length - 1];
```



## 5.4 创建元素节点

```js
let node = document.createElement('标签名')
```



## 5.5 将新创建的节点插入到父元素中

```js
let parent = document.getElementById('parent');
let node = document.createElement('p');
node.innerHTML = '这是一个p元素';
// 法一，直接插入到父元素的末尾
parent.appendChild(node);
// 法二，插入到父元素的某个子元素之前
parent.insertBefore(node, parent.children[0]);
```



## 5.6 删除元素节点

```js
parent.removeChild(parent.children[0]);
```



## 5.7 克隆节点

```js
// flag 为空 或 false 为浅拷贝只拷贝了标签，不拷贝子节点，flag为true时为深拷贝，拷贝标签和子节点
let newNode = node.cloneNode();
```

