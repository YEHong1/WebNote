## 1.安装

```js
// react-router-dom 是 react-router 的封装
npm i react-router-dom --save
```



## 2.基本使用

```jsx
// 入口文件 index.jsx
import React from "react";
import {render} from "react-dom";
import App from "./views/App/App";
//使用 BrowserRouter(history模式) 或 HashRouter(hash模式) 标签来包裹 App 组件
// 更推荐使用HashRouter， 使用BrowserRouter的话需要避开后端的路由
import {BrowserRouter} from "react-router-dom";
import './index.css'

render((
    <BrowserRouter>
        <App/>
    </BrowserRouter>
), document.getElementById('root'));
```



```jsx
// 创建 Home 和 About 组件
//Home.jsx
import React,{Component} from "react";
export default class Home extends Component{
    constructor(props) {
        super(props);
    }

    render() {
        return(
            <div>
                <h1>Home Component</h1>
            </div>
        )
    }
}

//About.jsx
import React,{Component} from "react";
export default class About extends Component{
    constructor(props) {
        super(props);
    }

    render() {
        return(
            <div>
                <h1>About Component</h1>
            </div>
        )
    }
}


```



```jsx
//在App组件中使用路由
import React, {Component} from "react";
// NavLink 最终会被转换为 a 标签， 使用 to 属性指向一个 path， 它是Link的封装
// Switch 用来包裹多个 Route 标签 显示符合条件的 Route对应的组件
// Route 拥有 path 属性和 component 属性， path 和 NavLink 的 to 属性一一对应
// Redirect 重定向， 拥有 to 属性
import {NavLink, Switch, Route, Redirect} from 'react-router-dom'
import Home from "../Home/Home";
import About from "../About/About";
import './App.css'

export default class App extends Component{
    // eslint-disable-next-line no-useless-constructor
    constructor(props) {
        super(props);
    }

    render() {
        return(
            <div className={'app'}>
                {/*header部分*/}
                <div className={'header'}>
                    <h1>Header</h1>
                </div>

                <div className={'content'}>
                    <ul className={'left'}>
                        <li>
                            <NavLink to={'/home'}>Home</NavLink>
                        </li>
                        <li>
                            <NavLink to={'/about'}>About</NavLink>
                        </li>
                    </ul>
                    <div className={'right'}>
                        <Switch>
                            <Route path='/home' component={Home}/>
                            <Route path='/about' component={About}/>
                            <Redirect to='/home'/>
                        </Switch>
                    </div>
                </div>
            </div>

        )
    }
}

```



```jsx
// 路由被选中问题
// NavLink 有一个 activeClassName 属性， 用来指定当前路由被选中时的类名
<NavLink to='/home' activeClassName='active'/>

//一般我们会封装一下 NavLink， 不然每一次使用NavLink时都需要写activeClassName属性
import React, {Component} from "react";
import {NavLink} from "react-router-dom";

export default class MyNavLink extends Component{
    render() {
        //将外部传入的所有属性传递给NavLink
        return <NavLink {...this.props} activeClassName={'active'}/>
    }
}

//使用的时候，导入MyNavLink组件来代替NavLink即可
```



## 3.路由传参

```jsx
// 使用占位符传递参数
<Route path='/home/news/detail/:id'/>

// 获取
let id = this.props.match.params.id
```



## 4.路由跳转的两种方式

```jsx
// 1.push 点击路由标签也是push的方式
this.props.history.push('/home')

// 2.replace
this.props.history.replace('/home')

// 区别
// 路由跳转历史  /home  =>  /home/message

// 使用push跳转到 /home/message/messageDetail
// 点击回退按钮，回退到 /home/message

// 使用replace跳转到 /home/message/messageDetail
// 点击回退按钮，回退到 /home
```



## 5.路由的前进和后退

```jsx
//前进
this.props.history.goForward()

//后退
this.props.history.goBack()
```

