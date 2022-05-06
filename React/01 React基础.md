## 1.1 React简介

### React的起源和发展

>    React 起源于 Facebook 的内部项目，因为该公司对市场上所有 JavaScript MVC 框架，都不满意，就决定自己写一套，用来架设Instagram 的网站。做出来以后，发现这套东西很好用，就在2013年5月开源了 

补充一下为什么虚拟DOM框架越来越流行：

-   原生js操作DOM繁琐、且效率低
-   js直接操作DOM会导致大量的重绘、重排，比较消耗性能
-   原生js没有组件化概念，代码复用率低（ 模块化只能复用js，无法复用HTML和css ）



## 1.2 React与传统MVC的关系

### 什么是MVC

mvc是一种设计模式， 由  模型（model）、 视图（view）、 控制器（controller ）三部分组成

-   model（模型）是应用程序中用于处理数据逻辑的部分，是应用的核心
-   view（视图）用于渲染数据
-   Controller（控制器）是应用程序中处理用户交互的部分

### React是属于MVC模式吗？

先说结论： React不是一个完整的MVC框架，最多可以认为是MVC中的V

一般情况下我们讲 MVC 都是在将系统视为整体。而 React 是基于组件的，组件就是渲染页面的基础，不论组件中包含的jsx，methods，state，props，都是属于组件内部的。一个组件可以视为一个局部的 MVC。

React 本身是没有状态管理的概念的，这也是为什么会有 redux 这类东西的存在。



## 1.3 React高性能的体现 - 虚拟DOM

>   在Web开发中我们总需要将变化的数据实时反应到UI上，这时就需要对DOM进行操作。而复杂或频繁的DOM操作通常是性能瓶颈产生的原因。（***重绘与回流***）
>
>   React为此引入了虚拟DOM（Virtual DOM）的机制：在浏览器端用Javascript实现了一套DOM API。基于React进行开发时所有的DOM构造都是通过虚拟DOM进行，每当数据变化时，React都会重新构建整个DOM树，然后React将当前整个DOM树和上一次的DOM树进行对比，得到DOM结构的区别，然后仅仅将需要变化的部分进行实际的浏览器DOM更新。而且React能够批处理虚拟DOM的刷新，在一个事件循环（Event Loop）内的两次数据变化会被合并，例如你连续的先将节点内容从A-B,B-A，React会认为A变成B，然后又从B变成A  UI不发生任何变化，而如果通过手动控制，这种逻辑通常是极其复杂的。
>
>   尽管每一次都需要构造完整的虚拟DOM树，但是因为虚拟DOM是内存数据，性能是极高的，故而对实际DOM进行操作的仅仅是Diff部分，因而能达到提高性能的目的。这样，在保证性能的同时，开发者将不再需要关注某个数据的变化如何更新到一个或多个具体的DOM元素，而只需要关心在任意一个数据状态下，整个界面是如何Render的。



#### 传统DOM操作示意图

![](.\images\01.png)



#### 虚拟DOM操作示意图

![](.\images\02.png)





## 1.4  React的 JSX

```jsx
// 1.创建react元素
// 参数1 元素名称
// 参数2 元素属性
// 第三个及以后的参数都为元素的子节点
// 需要安装react npm i react
let title = React.createElement('h1', {id: 'title'}, 'hello react');

// createElement()的问题
// 1.繁琐不简洁	2.不直观，不能一眼看出描述的结构

// jsx 是 React.createElement 的语法糖，用于描述虚拟DOM结构的
const vDom = <h1 id='title'>Hello World</h1>

// 2.渲染react元素
// 参数1 要渲染的react元素
// 参数2 挂载点
// 需要安装 react-dom  npm i react-dom
ReactDom.render(title, document.getElementById('root'));

// jsx语法在打包阶段已经被编译成JavaScript纯对象，这个过程由Babel的jsx编译器实现
jsx --> babel 编译 --> JavaScript对象 --> ReactDom.render() --> 将JavaScript对象渲染成DOM元素并插入到页面
```



## 1.5 React的特点

-   **声明式设计** - 采用声明范式，可以轻松描述应用 
-   **高效** - 通过对DOM的模拟，最大限度的减少与DOM的交互
-   **灵活** - 可以与已知的库或框架很好的配合
-   **JSX** - 使用JSX语法来描述DOM结构
-   **组件** - 通过构建组件，使得代码更加容易得到复用，能够很好的应用在大项目的开发中。
-   **单向数据流** - React 实现了单向响应的数据流（父组件流向子组件的数据，子组件不允许修改），从而减少了重复代码，这也是它为什么比传统数据绑定更简单。



## 2.1 JSX的介绍和规则

jsx全称为 JavaScript XML， 是 React.createElement 的语法糖，它的值不是一个字符串或者标签，而是一个形容 DOM 结构 的 js对象

1.只有一个根标签

```jsx
// 如果不希望根标签被渲染，可以使用Fragment来作为根标签
import React, {Component, Fragment} from 'react';

export default class App extends Component {
    constructor(props) {
        super(props);
        this.state = {};
    }

    render() {
        return (
            <Fragment>
                <p>Hello World</p>
                <p>App Component</p>
            </Fragment>
        )
    }
}
```

2.单标签必须闭合，如 <input type='text' />	<img src=''/>

3.img 标签必须包含 alt 属性

4.普通标签都是小写子母，组件标签首字母大写

5.标签的类名属性不是 class 而是 className

6.label 标签和 input 标签的绑定不是 for 而是  htmlFor

```jsx
<label htmlFor='userName'>
	<input id='userName' type='text' />
</label>
```

7.style 需要写成对象的形式

```jsx
// { color: 'red', fontSize: '60px' } 是style的写法，外面还有一层{}表示这是一个表达式
// 在react中插值表达式为{} Vue中的插值表达式则为{{}}
const element = <h1 style={{ color: 'red', fontSize: '60px' }}>Hello, Josh Perez</h1>;

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

8.表达式采用单括号，而非vue的双括号

9.不使用转义

>    React DOM 在渲染所有输入内容之前，默认会进行转义。它可以确保在你的应用中，永远不会注入那些并非自己明确编写的内容。所有的内容在渲染之前都被转换成了字符串。这样可以有效地防止 XSS（cross-site-scripting, 跨站脚本）攻击。 

```jsx
// 如果不希望转义的话，需要使用 dangerouslySetInnerHTML 属性
const name = '<p>Josh Perez</p>';
const element = (
  <h1>
    Hello, 
    <div dangerouslySetInnerHTML={{__html: name}}></div>
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

10.JSX其实是  **React.createElement()** 的语法糖，所以在React中凡是使用JSX语法的文件都需要在文件开头引入React

11.false、true、undefined、null都是合法的子元素，但它们都并不会被渲染。利用这个特性，可以通过三元运算符来控制是否显示对应组件

```jsx
// 当flag为true时渲染 <Swiper/> 组件, 为false时什么都不渲染
{
    flag ? <Swiper/> : false
}
```



## 3.1 组件

组件可以将  页面 切分成一些独立的、可复用的部件，这样你就只需专注于构建每一个单独的部件 。组件化可以复用 HTML、CSS、JS

-   组件从概念上看就像是函数，它可以接收任意的输入值（称之为“props”），并返回一个需要在页面上展示的React元素
-   React 组件没有全局组件的概念，需要使用某个组件的文件，都需要在当前文件定义好或者从其它文件中引入
-   React 组件首字母大写



## 3.2 组件的两种定义方式

1.函数式组件

```jsx
import React from 'react';
const Welcome = (props) => {
  return <h1>Hello, {props.name}</h1>;
}
```

2.类组件

```jsx
impoet React from 'react';
class Welcome extends React.Component{
    render(){
        return <h1>Hello,{this.props.name}</h1>
    }
}
```



## 3.3 组件的使用和嵌套

组件的使用

```jsx
ReactDOM.render(
  <div>
    <Component1 />
    <Component2 />
  </div>,
  document.getElementById('root')
);
```

组件嵌套

```jsx
// com1.js
class Com1 extends Component {
  render() {
    return (
      <div>我是 com1 组件</div>
    )
  }
}

// com2.js
import Com1 from './com1.js';
class Com2 extends Component {
  render() {
    return (
      <div>
        <h1>我是 com2 组件</h1>
        <Com1 />
      </div>
    )
  }
}
```





## 4. props

当React遇到的元素是用户自定义的组件，它会将JSX属性作为单个对象传递给该组件，这个对象称之为Props。Props中的每一项称之为一个prop 

### Props的特点

1.  props 是只读的。不允许修改。 

2.  props可以接受任意类型的值

3.  使用类组件时，如果写了构造函数，应该将 props 传递给 super() ,否则无法在构造函数中获得props

    ```jsx
    import React, {Component} from 'react';
    
    export default class App extends Component {
        constructor() {
            super();
            console.log(this.props); // undefined
        }
    
        render() {
            return (
                <h1>Hello world</h1>
            )
        }
    }
    ```




### Props 的类型校验 

文档：https://react.docschina.org/docs/typechecking-with-proptypes.html

1.需要安装 prop-types 模块

2.定义组件的 **propTypes** 属性

```jsx
import PropTypes from 'prop-types';

//类组件
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

Welcome.propTypes = {
  name: PropTypes.string
}

// 函数组件
const Hello = (props) => <h1>hello, { props.name }</h1>

Hello.propTypes = {
  name: PropTypes.number
}
```



### props的默认值

定义组件的 **defaultProps** 属性

```jsx
// 类组件
class Welcome extends React.Component {
    render() {  
        return <h1>Hello, {this.props.name}</h1>; 
    }
}

Welcome.defaultProps = {  
    name: '张三'
}

// 函数组件
const Hello = (props) => <h1>hello, { props.name }</h1>
Hello.defaultProps = { name: '张三' }
```



## 5. state

state 与 props 类似，但是 state 是私有的，并且完全受控于当前组件。 

### 初始化、使用与更新

```jsx
class Hello extends React.Component {
  constructor(props) {
    super(props);

    // 定义状态
    this.state = {
      name: "hello"
    };
  }

  render() {
    return (
      <div>
        {/* 通过 this.state.xxx 使用状态 */}
        <h1>hello, {this.state.name}</h1>
      
        {/* 通过调用 this.setState() 来更新状态 */}
        <button
          onClick={() => {
            this.setState({ name: "world" });
          }}
        >
          修改name
        </button>
      </div>
    );
  }
}
```



### 正确的使用 State

  1.不能直接通过this.state.xx = xx 来改变数据

2.  State 的更新可能是异步的

    ```jsx
    // 在一般情况下，setState 是异步的
    // 点击前count值为0
    handleClick = ()=>{
        this.setState({
            count: this.state.count + 1
        }, ()=>{
            console.log('callback', this.state.count) // count为1
        });
        console.log(this.state.count); // count为0，此处setState为异步
    };
    
    
    // 在setTimeout中， setState时同步的
    handleClick = ()=>{
        setTimeout(()=>{
            this.setState({
                count: this.state.count + 1
            });
            console.log(this.state.count); // count值为1， 此处setState为同步
        }, 0)
    };
    
    
    // 自己定义的Dom事件中，setState是同步的
    componentDidMount() {
        document.addEventListener('click', ()=>{
            this.setState({
                count: this.state.count + 1
            });
            console.log(this.state.count) // count值为1， 此处setState为同步
        })
    }
    ```

3.  State 的更新会被合并 

    ```jsx
    // 传入对象，会被合并。下面的结果为 count + 1
    // 因为setState在这种情况下为异步， 所以在第一次setState后，
    // this.staet.count还是+1之前的值，后面执行多少次结果都一样
    this.setState({
        count: this.setState.count + 1
    })
    
    this.setState({
        count: this.setState.count + 1
    })
    
    this.setState({
        count: this.setState.count + 1
    })
    
    
    // 传入函数不会被合并, 下面的结果为count + 3
    this.setState((preState, props)=>{
        return {
            count: prestate.count + 1
        }
    })
    
    this.setState((preState, props)=>{
        return {
            count: prestate.count + 1
        }
    })
    
    this.setState((preState, props)=>{
        return {
            count: prestate.count + 1
        }
    })
    ```

    

4.  普通情况下只能在 constructor 构造函数中初始化 state。(考查: 实验性的 public class fields 语法)

5.  constructor 构造函数 必须在函数第一行调用 super() 来调用父类的构造函数，不然 this 指针会出问题。

6.  通过 this.state.xxx 来使用某个状态。

7.  通过 this.setState() 来更新状态。

8.  state 或 props 的变化，会引起组件的重新 render 。

    -   对类组件来说，就是类的 render 函数重新执行
    -   对函数式组件来说，这个函数重新执行



## 6. React 中的this 问题

先回顾一下基础知识

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
  <script>
    function fn1(){
      // 非严格模式下 this指向 window， 严格模式下指向 undefined
      console.log(this)
    }
    fn1();
  </script>
</body>
</html>

```

类相关知识

```js
// 定义一个 Person 类
class Person{

  // 如果需要接受参数，需要定义 constructor 方法
  // constructor 中 this 指向 该类的实例对象
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  // 想给这个类添加一个 speak 的功能， 这个函数被定义在 类 的原型对象上
  // speak 中的 this 指向它的调用者
  speak(){
    console.log(`我叫${this.name}, 今年${this.age}岁`)
  }

}

const p1 = new Person('张三', 25);
p1.speak();


// 创建一个 Student 类， 继承 Person
class Student extends Person{

  // 我们可以不定义 constructor, 当创建Student实例时，会自动调用 Person 的 constructor 方法
  // 但是，如果我们需要给Student 添加其他属性时， 如果定义了 constructor， 就必须调用 super
  // super() 其实就是调用了Person 的 constructor, 注意： super() 需要放在 第一行
  constructor(name, age, studentId) {
    super(name, age);
    this.studentId = studentId;
  }

}

const student = new Student('王武', 18, 'stuId007');
console.log(student)
```



我们有遇到过，在组件中定义函数并调用，发现它的this指向undefined，而不是当前组件，这是为什么呢？请看下面示例

```js
import React, { Component } from "react";

export default class App extends Component{

  constructor() {
    super();
    this.state = { name: '张三', age: 18 }
  }

  sayHello(){
    /*
    * this 指向 undefined， 这是为什么呢？
    * 这就和 ReactDOM 的 render 流程有关，
    * ReactDOM.render 时，他会先解析组件。如果组件是使用类定义的，
    * 它会先 new 一个该类的实例出来，并通过该实例调用 原型对象上 的 render 方法
    * 最后将 render 返回的虚拟DOM渲染成真实DOM，呈现在页面中。
    *
    * 重点就在于 调用 实例的render 这一步， 请看下方 Person类代码
    * */
    console.log(this)
  }

  render() {
    return(
      <span onClick={this.sayHello}>Hello App</span>
    )
  }
}


class Person {
  render(){
    console.log(this)
  }
}

const p1 = new Person();
// 这时候render函数中 this 指向 p1
p1.render();
// 但是 react 中，onClick={fn}, fn是一个变量，它不是通过实例去调用的，而是直接调用的，可以理解为以下代码
const componentRender = p1.render;
// 这时候在严格模式下， render 中的this 指向 undefined
componentRender();
```



### 解决this指向问题的办法

1.  直接在事件绑定的地方加上 .bind(this) ，这种写法不推荐，因为每一次重新渲染都需要重新bind一次

    ```jsx
    <button onClick={this.handleClick.bind(this)}>点我</button>
    ```

2.  使用箭头函数 

    ```jsx
    <button onClick={()=>{
         this.handleClick()
    }}>点我</button>
    ```

    

3.  在构造函数中统一进行this指向的绑定 

    ```jsx
    constructor() {
        super();
        // constructor 中 this 必定指向当前实例对象
        this.handleClick = this.handleClick.bind(this);
    }
    
    render() {
        return (
            <button onClick={this.handleClick}>点我</button>
        )
    }
    ```

    

## 7. ref

ref 可以获取 DOM 或者 组件实例， 

**默认情况下，不能在函数组件上使用 ref 属性**，因为它们没有实例

如果要在函数组件中使用 ref，你可以使用 forwardRef useImperativeHandle 结合使用， hook篇有用法

```js
// 1.React.createRef() 
import React, { Component } from "react";

export default class App extends Component{

  spanRef = React.createRef();

  componentDidMount() {
    console.log(this.spanRef.current)
    // 打印 <span>Hello App</span>
  }

  render() {
    return(
      <span ref={this.spanRef}>Hello App</span>
    )
  }
}


// 2.回调形式
import React, { Component } from "react";

export default class App extends Component{

  componentDidMount() {
    console.log(this.spanRef)
    // 打印 <span>Hello App</span>
  }

  render() {
    return(
      <span ref={ref => this.spanRef = ref}>Hello App</span>
    )
  }
}

```



## 8.React 中的事件处理

我们发现 react 中的事件监听和原生的名称并不一致，例如 原生是 onclick， react 中 变成了 小驼峰命名 onClick。

React 使用的是自定义(合成)事件, 而不是使用的原生DOM事件，那么它和原生事件有什么区别呢？

-   添加了浏览器兼容处理
-   react 事件是通过事件委托处理的，把事件委托给最外层元素，这样做是为了提高效率



## 9.组件间通信

- 父子组件通信：

  ```js
  // 1.传递数据(父传子)与传递方法(子传父)
  // 2.ref标记 (父组件拿到子组件的引用，从而调用子组件的方法)
  ```

- 非父子组件间通信

  1. 状态提升
  
     将多个组件需要共享的state提升到它们最近的父组件上，在父组件上改变状态，通过props分发给子组件
  
     
  
  2. context
  
     缺点：当需要接收context数据的组件(A)的某个上级组件 shouldComponentUpdate 返回false时，context的更新不会引起下级组件(A)的更新
  
     
  
  3. 发布订阅模式
  
     以b站平台为例，你关注了一个up主，当这个up主发布新视频时，你将得到平台的消息提醒，这就是典型的发布订阅模式
  
     ```js
     // 先写一个原生JavaScript的代码写法
     const eventBus = {
       // 存放被订阅的事件回调函数
       state: {
         // key 对应时间名称， value为一个数组，用来存放对应的监听函数列表
         message: [],
       },
       // 订阅 传递订阅的时间类型和回调函数
       subscribe(eventName, callback) {
         // 添加到state中
         const list = this.state[eventName] || [];
         this.state[eventName] = [...list, callback];
       },
       // 发布，触发对应的订阅事件
       publish(eventName, value) {
         const list = this.state[eventName];
         list.forEach((callback) => callback && callback(value));
       },
     };
     
     // 订阅事件 submit
     eventBus.subscribe("submit", (value) => console.log(value));
     
     // 发布，触发时间 submit
     eventBus.publish("submit", "submit时间被触发");
     // 输出 submit时间被触发
     ```
  
     ```js
     // react 中的用法
     1.新建一个 eventBus.js
     import { EventEmitter } from "events";
     export default new EventEmitter();
     
     2.在组件A中订阅事件
     import eventBus from "@/eventBus";
     // 事件名称、回调函数
     eventBus.addListener("message", (message) => {
       alert(message);
     });
     
     // 如果需要取消监听，用removeListener
     eventBus.removeListener("message");
     
     3.在另一个组件中触发事件触传递信息
     import eventBus from "@/eventBus";
     // 事件名称、传递的数据
     eventBus.emit("message", "hello world");
     ```
  
     
  
  

## 10.高阶函数

说高阶组件前，先来理解一下什么是高阶函数

##### 高阶函数

将函数作为参数，或者将函数作为返回值返回的函数叫做高阶函数

同理，将组件作为参数，或者将组件作为返回值返回的组件，称为高阶组件



再补充一个函数柯里化

函数柯里化其实是高阶函数的一种特殊用法，它接受函数A作为参数，返回一个新的函数，并且这个新函数能够处理函数A的剩余参数。



## 11.生命周期

随着React 17的更新，React的生命周期也发生了些许改变，我们先来复习一下旧版本的生命    周期

### 旧版本生命周期

![](.\images\生命周期（旧）.png)



### 生命周期的三个阶段



### 1.创建时（挂载阶段）

-   执行时机：组件被创建时（页面在加载时）
-   执行顺序： constructor      componentWillMount()      render      componentDidMount

```js
// constructor
constructor()中完成了React数据的初始化，它接受两个参数：props和context，当想在函数内部使用这两个参数时，需使用super()传入这两个参数。
注意：只要使用了constructor()就必须写super(),否则会导致this指向错误。

// componentWillMount
componentWillMount()一般用的比较少，它更多的是在服务端渲染时使用。它代表的过程是组件已经经历了constructor()初始化数据后，但是还未渲染DOM时。 

// render
render函数会插入jsx生成的dom结构，react会生成一份虚拟dom树，在每一次组件更新时，在此react会通过其diff算法比较更新前后的新旧DOM树，比较以后，找到最小的有差异的DOM节点，并重新渲染。

// componentDidMount
组件第一次渲染完成，此时dom节点已经生成，可以在这里调用ajax请求，返回数据setState后组件会重新渲染

```



### 2.更新阶段

以下三种情况会导致更新：

-    setState()
-    接受的props发生改变
-    组件调用 forceUpdate() 强制更新组件

```js
// componentWillReceiveProps (nextProps)
在接受父组件改变后的props需要重新渲染组件时用到的比较多
接受一个参数nextProps
通过对比nextProps和this.props，将nextProps的state为当前组件的state，从而重新渲染组件


// shouldComponentUpdate(nextProps,nextState)
主要用于性能优化(部分更新)
唯一用于控制组件重新渲染的生命周期，由于在react中，setState以后，state发生变化，组件会进入重新渲染的流程，在这里return false可以阻止组件的更新
因为react父组件的重新渲染会导致其所有子组件的重新渲染，这个时候其实我们是不需要所有子组件都跟着重新渲染的，因此需要在子组件的该生命周期中做判断

// componentWillUpdate (nextProps,nextState)
shouldComponentUpdate返回true以后，组件即进入重新渲染的流程，进入componentWillUpdate,这里同样可以拿到nextProps和nextState。

// render

// componentDidUpdate(prevProps,prevState)
组件更新完毕后，react只会在第一次初始化成功会进入componentDidmount,之后每次重新渲染后都会进入这个生命周期，这里可以拿到prevProps和prevState，即更新前的props和state。

```



### 3.卸载阶段

```js
// componentWillUnmount 
在此处完成组件的卸载和数据的销毁。

1.clear你在组件中所有的setTimeout,setInterval
2.移除所有组件中的监听 removeEventListener
3.有时候我们会碰到这个warning:
Can only update a mounted or mounting component. This usually means you called setState() on an unmounted component. This is a   no-op. Please check the code for the undefined component.

原因：因为你在组件中的ajax请求返回setState,而你组件销毁的时候，请求还未完成，因此会报warning
解决方法：

componentDidMount() {
    this.isMount === true
    axios.post().then((res) => {
        this.isMount && this.setState({   // 增加条件ismount为true时
              aaa:res
            })
    })
}

componentWillUnmount() {
    this.isMount === false
}
```













