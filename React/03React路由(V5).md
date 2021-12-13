# React 路由

## 1.路由介绍

```js
1.什么是路由
路由是根据不同的 url 地址展示不同的页面或内容

2.什么是前端路由
修改Url后，页面不刷新同时可以展示不同的界面。
// 优点
用户体验好，和后台网速没有关系，不需要每次都从服务器全部获取，快速展现给用户
// 缺点
使用浏览器的前进，后退键的时候会重新发送请求，没有合理地利用缓存


3.什么是后端路由
浏览器在地址栏中切换不同的url时，每次都向后台服务器发出请求，服务器响应请求，在后台拼接html文件传给前端显示, 返回不同的页面。
// 优点
分担了前端的压力，html和数据的拼接都是由服务器完成。
// 缺点
当项目十分庞大时，加大了服务器端的压力，同时在浏览器端不能输入指定的url路径进行指定模块的访问。
另外一个就是如果当前网速过慢，那将会延迟页面的加载，造成空白页面，对用户体验不是很友好
```



## 2.前端路由原理

 原理：监听URL的变化，判断当前的URL，决定渲染的内容

```js
有2种模式，一种是Hash模式，另一种是History模式

1.Hash模式原理
// URL的hash也就是锚点('#'), 本质上是改变window.location的href属性
// 通过赋值location.hash来改变href时，不会导致页面刷新。

// 优点
兼容性好，IE也支持
// 缺点
网页地址带有'#'号，不像一个真实的路劲

```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <!--通过锚点来改变hash-->
        <a href="#/home">首页</a>
        <a href="#/about">关于</a>
    
        <div class="router-view"></div>
    </div>
    
    <script>
        // 获取router-view的DOM
        const routerViewEl = document.querySelector('.router-view');
    
        // 监听URL的改变
        window.addEventListener("hashchange", () => {
            switch (location.hash) {
                case "#/home":
                    routerViewEl.innerHTML = "首页";
                    break;
                case "#/about":
                    routerViewEl.innerHTML = "关于";
                    break;
                default:
                    routerViewEl.innerHTML = "";
            }
        })
    </script>
</body>
</html>
```



```js
2.History模式原理
// history接口是HTML5新增的, 它有6种模式改变URL而不刷新页面
replaceState：替换原来的路径
pushState：使用新的路径
popState：路径的回退
go：向前或向后改变路径
forward：向前改变路径
back：向后改变路径

```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <a href="/home">首页</a>
        <a href="/about">关于</a>

        <div class="router-view"></div>
    </div>

    <script>
        // 1.获取router-view的DOM
        const routerViewEl = document.querySelector('.router-view');

        // 获取所有的a元素, 监听点击事件并阻止默认行为（阻止网页跳转刷新）
        const aEls = document.getElementsByTagName("a");
        for(let i=0; i<aEls.length; i++){
            const el = aEls[i];
            el.addEventListener("click", e => {
                e.preventDefault();
                const href = el.getAttribute("href");
                history.pushState({}, "", href);
                urlChange();
            })
        }

        // 用户点击浏览器前进后退触发popstate监听
        window.addEventListener('popstate',urlChange);
        
        // 监听URL的改变
        function urlChange() {
            switch (location.pathname) {
                case "/home":
                    routerViewEl.innerHTML = "首页";
                    break;
                case "/about":
                    routerViewEl.innerHTML = "关于";
                    break;
                default:
                    routerViewEl.innerHTML = "";
            }
        }

    </script>
</body>
</html>
```



## 3. 介绍react-router

 React Router的版本4开始，路由不再集中在一个包中进行管理了 ，而是分成3部分

-    react-router是router的核心部分代码 
-    `react-router-dom`是用于浏览器的 
-    react-router-native是用于原生应用的 

我们开发web端时直接安装 `react-router-dom`即可，它依赖于react-router。

```js
yarn add react-router-dom@5.3.0
```



## 4. router的基本组件与API

```jsx
// react-router最主要的API是给我们提供的一些组件
1.BrowserRouter或HashRouter
Router中包含了对路径改变的监听，并且会将相应的路径传递给子组件
BrowserRouter使用history模式
HashRouter使用hash模式

2.Link和NavLink
通常路径的跳转是使用Link组件，最终会被渲染成a元素
to属性：Link中最重要的属性，用于设置跳转到的路径
NavLink是在Link基础之上增加了一些样式属性

// NavLink的属性
activeStyle：活跃时（匹配时）的样式
activeClassName：活跃时添加的class，默认样式名为.active
exact：是否精准匹配

// 当一个标签需要写多个类名且存在变量时在className中用模板字符串隔开即可
'<p className={`class1 class2 ${Date.now() % 2 === 0 ? 'activeClass' : ''}`}>
	count: 1
</p>'

3.Route
Route用于路径的匹配
path属性：用于设置匹配到的路径
component属性：设置匹配到路径后，渲染的组件
exact：精准匹配，只有精准匹配到完全一致的路径，才会渲染对应的组件

4.Switch
渲染与该地址匹配的第一个子节点 <Route> 或者 <Redirect>
当我们匹配到某一个路径时，可能匹配到多个符合条件的Route，例如 
<>
    <Route patch={'/about'} component={AboutComponent}/>
	<Route patch={'/about/:userid'} component={AboutComponent}/>
</>
react-router中只要是路径被匹配到的Route对应的组件都会被渲染，而我们只希望渲染其中一个
这个时候就可以使用Switch标签把所有的Route包裹起来，这样就只会匹配到第一个Route


4.Redirect
Redirect用于路由的重定向，当这个组件出现时，就会执行跳转到对应的to路径中

// 上面是组件，下面是api

5.withRouter
当一个组件不是路由组件，但是我们又希望他能够在组件内部使用路由的方法时，可以使用withRouter这个高阶函数
export default withRouter(组件) 即可

6.history
// history 对象通常会具有以下属性和方法：
length - (number 类型) history 堆栈的条目数
action - (string 类型) 当前的操作(PUSH, REPLACE, POP)
location - (object 类型) 当前的位置。location 会具有以下属性：
	pathname - (string 类型) URL 路径
	search - (string 类型) URL 中的查询字符串
	hash - (string 类型) URL 的哈希片段
	state - (object 类型) 提供给例如使用 push(path, state) 操作将 location 放入堆栈时的特定 location状态。只在浏览器和内存历史中可用。
push(path, [state]) - (function 类型) 在 history 堆栈添加一个新条目
replace(path, [state]) - (function 类型) 替换在 history 堆栈中的当前条目
go(n) - (function 类型) 将 history 堆栈中的指针调整 n
goBack() - (function 类型) 等同于 go(-1)
goForward() - (function 类型) 等同于 go(1)
block(prompt) - (function 类型) 阻止跳转。(详见 history 文档)。
// history 对象是可变的，因此我们建议从 <Route> 的渲染选项中来访问 location，而不是从 history.location 直接获取。这样做可以保证 React 在生命周期中的钩子函数正常执行

7.location
location 代表应用程序现在在哪，你想让它去哪，或者甚至它曾经在哪

8.match
一个 match 对象中包涵了有关如何匹配 URL 的信息
// 属性
params - (object) key／value 与动态路径的 URL 对应解析
isExact - (boolean) true 如果匹配整个 URL （没有结尾字符）
path - (string) 用于匹配的路径模式。被嵌套在 <Route> 中使用
url - (string) 用于匹配部分的 URL 。被嵌套在 <Link> 中使用

```



## 5.路由传参

```js
1.动态路由
// <Route path={'/game/:id'} component={Game}/>
// <NavLink to={`/game/${id}`} activeClassName={'linkActive'}>游戏</NavLink>
获取传递的参数
const {id} = this.props.match.params;

2.通过query传递
// <NavLink to={'/activity?activityName=天安门广场阅兵&date=2020-10-01'} activeClassName={'linkActive'}>活动</NavLink>
获取传递的数据
const {search} = this.props.location;
// 获取到的search需要自己处理
getQueryValueByName = (name)=>{
    let search = this.props.location.search.substring(1);
    let arr = search.split('&');
    for(let i=0; i<arr.length; i++){
        const keyValueArr = arr[i].split('=');
        if(keyValueArr[0] ===  name){
            return keyValueArr[1];
        }
    }
}


3.通过state传递，刷新界面后传递的数据将会丢失
// <NavLink to={{pathname: '/rank', state: {name: '123', age: 18} }} activeClassName={'linkActive'}>排行榜</NavLink>

this.props.history.push({
    pathname: '/rank',
    state: {
        name: '胡八一',
        age: 30,
        rankNum: 2
    }
})


// 通过location获取state传递过来的数据， 刷新后state数据会丢失
const {state = {}} = this.props.location;
```



## 6.路由跳转的两种方式

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



## 7.路由的前进后退

```js
//前进
this.props.history.goForward()

//后退
this.props.history.goBack()
```



## 8.Hook部分

### useHistory

`useHistory` 帮助我们直接访问到`history`,而不再需要通过 props 访问

```jsx
import { useHistory } from "react-router-dom";

const Contact = () => {
  const history = useHistory();
  return (
    <Fragment>
      <h1>Contact</h1>
      <button onClick={() => history.push("/")}>Go to home</button>
    </Fragment>
  );
};

```



### useParams

`useParams` 帮助我们直接访问到路由参数,而不再需要通过 props.match.params 访问

```jsx
// useParams 拿到的参数是 动态路由参数，不是query
const About = () => {
  const { name } = useParams();
  return (
    <h1>About {name}</h1>
  );
};

```



### useLocation

`useLocation` 会返回当前 URL 的 location 对象

```jsx
import { useLocation } from "react-router-dom";

const Contact = () => {
  const { pathname } = useLocation();

  return (
    <Fragment>
      <h1>Contact</h1>
      <p>Current URL: {pathname}</p>
    </Fragment>
  );
};

```



## 9.示例

```jsx
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import './resource/css/normaliza.css';
import {HashRouter} from 'react-router-dom';
import './index.css'

ReactDOM.render(
    <HashRouter>
        <App />
    </HashRouter>,
    document.getElementById('root')
);
```

```jsx
// App.js
import React, {Component} from 'react';
import {NavLink, Switch, Route, Redirect} from 'react-router-dom';
import Home from "./components/Home/Home";
import Chat from "./components/Chat/Chat";
import Game from "./components/Game/Game";
import Activity from "./components/Activity/Activity";
import Rank from "./components/Rank/Rank";

export default class App extends Component {
    constructor(props) {
        super(props);
        this.state = {
            id: 'QAD123456',
            peopleInfo: {
                name: '张三',
                age: 18,
                rankNum: 1
            }
        }
    }

    render() {
        const {id, peopleInfo} = this.state;
        return (
            <>
                <div style={{height: 88, backgroundColor: 'skyblue', display: 'flex', justifyContent: 'space-around', alignItems: 'center'}}>
                    {/*
                    NavLink被选中时会有一个类名为active， 这里使用activeClassName来修改，也可以使用activeStyle来修改选中样式
                    匹配到其它NavLink时，首页这个也符合条件，这里加一个exact来精准匹配。
                    */}
                    <NavLink exact to={'/'} activeClassName={'linkActive'}>首页</NavLink>
                    <NavLink to={'/chat'} activeClassName={'linkActive'}>聊天</NavLink>
                    {/*动态路由传递简单参数*/}
                    <NavLink to={`/game/${id}`} activeClassName={'linkActive'}>游戏</NavLink>
                    {/*search传递参数， query形式*/}
                    <NavLink to={'/activity?activityName=天安门广场阅兵&date=2020-10-01'} activeClassName={'linkActive'}>活动</NavLink>
                    {/*
                    NavLink的to属性可以传入字符串、对象、函数，这里通过state传递复杂数据
                    通过state传递的数据在刷新后会丢失
                    */}
                    <NavLink to={{
                        pathname: '/rank',
                        query: '',
                        search: '',
                        state: peopleInfo
                    }} activeClassName={'linkActive'}>排行榜</NavLink>
                </div>

                <div style={{height: 300, backgroundColor: 'pink'}}>
                    {/*Switch 匹配到符合条件的第一个，没有Switch的话会显示所有符合匹配规则的Route*/}
                    <Switch>
                        {/*exact 精准匹配*/}
                        <Route exact path={'/'} component={Home}/>
                        <Route path={'/chat'} component={Chat}/>
                        {/*动态路由， /:id */}
                        <Route path={'/game/:id'} component={Game}/>
                        <Route path={'/activity'} component={Activity}/>
                        <Route path={'/rank'} component={Rank}/>
                        {/*重定向*/}
                        <Redirect to={'/'}/>
                    </Switch>
                </div>
            </>
        )
    }
}
```

```js
// Home.js
import React, {Component} from 'react';

export default class Home extends Component {
	render() {
		return (
			<div>Home</div>
		)
	}
}
```

```js
// Game.js
import React, {Component} from 'react';

export default class Game extends Component {

	render() {
		/*动态路由传递的参数使用this.props.match.params来获取*/
		const {id} = this.props.match.params;
		return (
			<div>
				<h2>Game</h2>
				<p>通过动态路由传递的参数为: {id}</p>
			</div>
		)
	}
}
```

```js
// Activity.js
import React, {Component} from 'react';

export default class Activity extends Component {

	render() {
		/*
		使用this.props.location.search来获取query，
		不要用this.props.history.location，因为history时可变的，具有不确定性
		获取到的search还需要自己处理一下 "?activityName=天安门广场阅兵&date=2020-10-01"
		*/
		// const {search} = this.props.location;

		return (
			<div>
				<h2>Activity</h2>
				<p>通过search接受到的参数: </p>
				<p>活动名称: {this.getQueryValueByName('activityName')}</p>
				<p>活动日期: {this.getQueryValueByName('date')}</p>
			</div>
		)
	}

	getQueryValueByName = (name)=>{
		/*decodeURIComponent对url进行解码*/
		let search = decodeURIComponent(this.props.location.search).substring(1);
		let arr = search.split('&');
		for(let i=0; i<arr.length; i++){
			const keyValueArr = arr[i].split('=');
			if(keyValueArr[0] ===  name){
				return keyValueArr[1];
			}
		}
	}
}
```

```js
// Rank.js
import React, {Component} from 'react';

export default class Rank extends Component {

	render() {
		/*通过location获取state传递过来的数据， 刷新后state数据会丢失*/
		const {state = {}} = this.props.location;
		return (
			<div>
				<h2>Rank</h2>
				<p>通过state传递的数据为:</p>
				<p>名称: {state.name}</p>
				<p>年龄: {state.age}</p>
				<p>排名: {state.rankNum}</p>
			</div>
		)
	}
}
```

```js
// Chat.js
import React, {Component} from 'react';
import ChatRouter from "./ChatRouter";

export default class Chat extends Component {

	render() {
		return (
			<>
				<h2 style={{marginTop: 0}}>Chat</h2>
				<div style={{display: 'flex', alignItems: 'center', justifyContent: 'space-between'}}>
					<div style={{
						width: '50%',
						height: 60,
						lineHeight: '60px',
						textAlign: 'center',
						backgroundColor: 'orange',
						color: '#fff'
					}} onClick={()=>{
						/*编程式导航*/
						this.props.history.push('/chat/newTopic');
					}}>
						最新话题
					</div>
					<div style={{
						width: '50%',
						height: 60,
						lineHeight: '60px',
						textAlign: 'center',
						backgroundColor: 'darkblue',
						color: '#fff'
					}} onClick={()=>{
						this.props.history.push('/chat/hotTopic');
					}}>
						热门话题
					</div>
				</div>
				{/*路由组件中的路由: 嵌套路由*/}
				<ChatRouter/>
			</>
		)
	}
}
```

```js
// ChatRouter.js
import React, {Component} from 'react';
import {Switch, Route, Redirect} from 'react-router-dom';
import NewTopic from "./ChatChildren/NewTopic/NewTopic";
import HotTopic from "./ChatChildren/HotTopic/HotTopic";

export default class ChatRouter extends Component {

	render() {
		return (
			<Switch>
				<Route path={'/chat/newTopic'} component={NewTopic}/>
				<Route path={'/chat/hotTopic'} component={HotTopic}/>
				<Redirect to={'/chat/newTopic'}/>
			</Switch>
		)
	}
}
```

```js
// NewTopic.js
import React, {Component} from 'react';

export default class NewTopic extends Component {
	
	render() {
		return (
			<div>NewTopic</div>
		)
	}
}
```

```js
// HotTopic
import React, {Component} from 'react';

export default class HotTopic extends Component {
	
	render() {
		return (
			<div>HotTopic</div>
		)
	}
}
```

