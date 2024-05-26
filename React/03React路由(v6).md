## 路由介绍

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

​																																																																		

## 前端路由原理

 原理：监听URL的变化，判断当前的URL，决定渲染的内容

```js
有2种模式，一种是Hash模式，另一种是History模式

1.Hash模式原理
// URL的hash也就是锚点('#'), 本质上是改变window.location的href属性
// 通过赋值location.hash来改变href时，不会导致页面刷新。

// 优点
兼容性好，IE也支持
// 缺点
网页地址带有'#'号，不像一个真实的路径

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



## 1.`v5`和`v6`的区别

```js
1.v6的包体大小比v5小了将近一半

2.<Route> 特性变更
path: 与当前页面对应的URL匹配
elemnt: 新增属性，用于决定路由匹配时渲染那个组件，替代了v5的component和render

3.<Routes> 代替了 <Switch>
  
4.<Outlet> 用来渲染子路由，类似vue的 <router-view />
  
5.移除了 <Redirect />， 重定向使用 <Navigate />

6.useNavigate 代替 useHistory

7.移除了 <NavLink> 的 activeClassName 和 activeStyle

8.可以使用 useRoutes 来进行 配置式路由
```



## 2.基础用法

```jsx
import { HashRouter, Routes, Route, Navigate } from "react-router-dom";
import HomePage from "./pages/HomePage";
import PersonalCenter from "./pages/PersonalCenter";

const App = () => {
  return (
    <HashRouter>
      {/* Routes 替代原来的 Switch */}
      <Routes>
        <Route path={"/index"} element={<HomePage />} />
        <Route path={"/mine"} element={<PersonalCenter />} />
        {/* 重定向 Redirect组件已被移除，可以使用Navigate来代替 */}
        <Route path={"/"} element={<Navigate to={"/index"} />} />
        {/* 404 */}
        <Route path={"*"} element={"Hello world"} />
      </Routes>
    </HashRouter>
  );
};

export default App;
```



## 3.嵌套路由

```jsx

<Route path={"/index"} element={<HomePage />}>
  <Route path={"a"} element={<HomeChildA />} />
  <Route path={"b"} element={<HomeChildB />} />
  <Route path={""} element={<Navigate to={"/index/a"} />} />
</Route>

// 在 HomePage 文件中 引入 Outlet，并渲染即可显示被嵌套的路由，和 vue的 <router-view/> 用法一致

```



## 4.声命式导航和编程式导航

```jsx
// 声命式导航
// 需要注意： 新版本 NavLink 移除了activeClassName 属性，取而代之的是className属性，他接收字符串或者函数作为属性值
<NavLink to={"/index/a"} className={'myActiveClassName'} >HomeChildA</NavLink>
<NavLink style={{ marginLeft: 18 }} to={"/index/b"} className={({ isActive })=> isActive ? 'active' : 'noActive'}>
  HomeChildB
</NavLink>


// 编程式导航
const navigate = useNavigate()
navigate('/index/b', { replace: true })
```



## 5.`useRouter`实现路由配置

```jsx
// src/router/index.tsx
import { RouteObject, Navigate } from 'react-router-dom';
import LayoutPage from "../pages/LayoutPage";
import Home from "../pages/Home";
import Login from "../pages/user/Login";
import UserInfo from "../pages/UserInfo";

const router: RouteObject[] = [
	{
		path: '/',
		element: <LayoutPage />,
		children: [
			{ path: '/index', element: <Home /> },
			{ path: '/userInfo/:id', element: <UserInfo /> },
            // 重定向
            { path: '/resetPath', element: <Navigate to={'/index'} /> },
		]
	},
	{ path: '/user/login', element: <Login /> },
	{ path: '*', element: 'Not Found' }
];

export default router;


// 在根组件处使用 useRoute 来渲染
import { useRoutes } from 'react-router-dom';
import router from "./routers";

const App = ()=>{
    // 注意，useRouter 这类hooks只能在函数顶层使用，HashRouter标签只能放在项目的入口文件里写了
	return useRoutes(router)
}

export default App;
```



## 添加路由懒加载

```tsx
// src/router/index.tsx
import { lazy, ReactElement, Suspense } from 'react';
import { RouteObject, Navigate } from 'react-router-dom';
import LayoutPage from "../pages/LayoutPage";
const Home = lazy(()=> import('../pages/Home'))
const Login = lazy(()=> import('../pages/user/Login'))
const UserInfo = lazy(()=> import('../pages/UserInfo'))

const LazyLoad = (lazyComponent: ReactElement, fallback: any = 'loading')=>{
	return(
		<Suspense fallback={fallback}>
			{lazyComponent}
		</Suspense>
	)
}

const router: RouteObject[] = [
	{
		path: '/',
		element: <LayoutPage />,
		children: [
			{ path: '/index', element: LazyLoad(<Home />) },
			{ path: '/userInfo/:id', element: LazyLoad(<UserInfo />) },
			{ path: '/resetPath', element: <Navigate to={'/index'} /> },
		]
	},
	{ path: '/user/login', element: LazyLoad(<Login />) },
	{ path: '*', element: 'Not Found' }
];

export default router;
```



## 6.获取路由参数

- 获取 `query`形式的参数

```jsx
// index?qq=870628496&name=abc&name=zxc
import { useSearchParams } from 'react-router-dom';

const Home = ()=>{
	// 主要用到 params的 get 和 getAll 方法
	const [params] = useSearchParams();
	console.log(params.get('qq'))
    // 获取第一个参数名称为name的参数值
	console.log(params.get('name'))  // abc
    // 获取是由参数名称为name的参数值，返回一个字符串数组
	console.log(params.getAll('name')) // ['abc', 'zxc']

	return(
		<div>
			<span>Home</span>
		</div>
	)
}

export default Home;

```



- 获取动态参数

```jsx
//  在路由中定义/userInfo/:userId
import { useParams } from 'react-router-dom';

const UserInfo = ()=>{
	const params = useParams();
	return(
		<div>{params.userId}</div>
	)
}

export default UserInfo;
```



## 7.路由监听

```tsx
// 可以在App组件中添加对路由地址的监听，从而实现一个全局路由守卫
import { useEffect } from "react";
import { useRoutes, useLocation } from 'react-router-dom';
import router from "./routers";

const App = ()=>{

	const location = useLocation();

	useEffect(()=>{
		console.log('当前路由', location.pathname);
		// 如果当前用户没有权限访问该路由时，可以重定向到其它路由
		return ()=>{
			console.log(`从路由${location.pathname}离开`)
		}
	}, [location.pathname])

	return (
		useRoutes(router)
	)
}

export default App;
```









