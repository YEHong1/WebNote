## 1.`v5`和`v6`的区别

```js
1.v6的包体大小比v5小了将近一半

2.<Route> 特性变更
path: 与当前页面对应的URL匹配
elemnt: 新增属性，用于决定路由匹配时渲染那个组件，替代了v5的component和render

3.<Routes> 代替了 <Switch>
  
4.<Outlet> 用来渲染子组件，路由嵌套更简单

5.useNavigate 代替 useHistory

6.移除了 <NavLink> 的 activeClassName 和 activeStyle

7.钩子useRoutes 代替 react-router-config的作用
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

```

