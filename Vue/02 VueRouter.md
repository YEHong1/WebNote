## 一. 路由原理（去react路由笔记看）



## 二. 路由基本配置

步骤1：安装路由插件 （注意： `vue2`的路由只能使用3，4是给`vue3`用的）

```shell
npm i vue-router@3 --save
```

步骤2： 配置路由信息

提前创建了 Home组件和About组件

创建router文件夹，在router文件夹中创建`index.js`来存放路由配置代码

```js
// router/index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Home from '../components/Home/Home'
// 所有的vue插件的使用都需要use一下
Vue.use(VueRouter);

const routes = [
    {
        // 对应当前路由的路径
        path: '/home',
        // 路由路径对应的组件，又称为路由组件
        component: Home
    },
    {
        path: '/about',
        // 使用 ()=> import('路由组件文件位置') 实现路由懒加载
        component: ()=> import('../components/About/About')，
        // 如果需要嵌套路由，使用children属性
        children: [
        	// 在About组件中添加<router-view/>来渲染嵌套的组件
        	// 这里的path可以省略 /about/
            {
                path: 'aboutChild',
                component: ()=> import('../components/About/AboutChild/AboutChild')
            }
        ]
    },
    {
        path: '*',
        // redirect 重定向
        redirect: '/home'
    }
];

export default new VueRouter({
    // routes对应路由映射信息，是一个数组
    routes,
    // 配置路由模式, 一般分为hash和history
    mode: 'hash',
    // 全局配置 <router-link> 默认的激活的 class。 默认值为 'router-link-active'
    linkActiveClass: 'router-link-active'
})

```

在项目入口文件main.js中 配置到`Vue`实例中

```js
// main.js
import Vue from 'vue'
import App from './App.vue'
import router from './router'

new Vue({
    router,
    render: h => h(App)
}).$mount('#app')
```

步骤3：在App组件中，使用<router-view/>渲染路由映射界面

```vue
<!--App.vue-->
<template>
    <div>
        <!-- router-link最终被渲染为a标签，使用to属性改变路由路径 -->
        <div class="nav">
            <router-link to="/home">首页</router-link>
            <router-link to="/about">关于</router-link>
        </div>
        <!-- router-view 渲染路由映射界面 -->
        <router-view/>
    </div>
</template>

<script>
export default {
    name: 'App',
}
</script>

<style scoped lang="less">
    .nav{
        display: flex;
        & > a{
            text-decoration: none;
            margin-left: 180px;
            color: #333;
        }
        a.router-link-active{
            color: orangered;
        }
    }
</style>
```



## 三.路由常用API、组件解析

## 1.router-link 组件

<router-link> 组件支持用户在具有路由功能的应用中 (点击) 导航。 通过 `to` 属性指定目标地址，默认渲染成带有正确链接的 `a` 标签，可以通过配置 `tag` 属性生成别的标签.。另外，当目标路由成功激活时，链接元素自动设置一个表示激活的 CSS 类名。 

```js
// props
1.to
表示目标路由的链接。当被点击后，内部会立刻把 to 的值传到 router.push()，所以这个值可以是一个字符串或者是描述目标位置的对象。

// <router-link to="/home">Home</router-link>
// <router-link :to="{ path: 'home' }">Home</router-link>
这里有个注意点：需要使用params传递参数时，需要用name来代替path，否则无法接受到参数。
使用name来跳转，需要在路由配置时添加name属性，这种叫命名式路由
// <router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
这里使用query来传递参数
// <router-link :to="{ path: 'register', query: { plan: 'private' }}">Register</router-link>

2.replace
默认值为false，布尔值类型
设置 replace 属性的话，当点击时，会调用 router.replace() 而不是 router.push()，于是导航后不会留下 history 记录。
// <router-link :to="{ path: '/abc'}" replace></router-link>

3.append
默认值为false，布尔值类型
设置 append 属性后，则在当前 (相对) 路径前添加基路径。例如，我们从 /a 导航到一个相对路径 b，如果没有配置 append，则路径为 /b，如果配了，则为 /a/b
// <router-link :to="{ path: 'relative/path'}" append></router-link>

4.tag
默认值为'a'，字符串类型
有时候想要 <router-link> 渲染成某种标签，例如 <li>。 于是我们使用 tag 指定何种标签，同样它还是会监听点击，触发导航。
// <router-link to="/foo" tag="li">foo</router-link>

5.active-class
默认值: "router-link-active"， 是一个string类型
设置链接激活时使用的 CSS 类名。默认值可以通过路由的构造选项 linkActiveClass 来全局配置。

6.exact
默认值为false，布尔值类型
是否触发精确匹配，只有在路由路径和to属性对应的值一致时路由标签才会被添加上对应的激活类名
例如 to='/user' 在路由路径为 /user/123456的情况下，不存在类名 router-link-active
```



## 2. router-view组件

<router-view>  组件是一个 `functional` 组件，渲染路径匹配到的视图组件。渲染的组件还可以内嵌自己 ，根据嵌套路径，渲染嵌套组件。 

我们需要在路由跳转的时候添加一些过度动画可以配合<transition>使用。

```html
<transition>
  <!-- 记得加上keep-alive -->
  <keep-alive>
    <router-view></router-view>
  </keep-alive>
</transition>
```

```js
// props
name
默认值为 'default'， 字符串类型， 对应路由名称
如果 <router-view>设置了名称，则会渲染对应的路由配置中 component 下的相应组件
```



## 3. $route 和 $router

```js
// 在vue组件中我们可以发现this下存在 $router 和 $route
1.$router
$router是全局路由器对象，是VueRouter的实例。主要用于路由跳转


2.$route
$route是当前路由对象，主要用来获取传递到当前组件的参数
一个路由对象表示当前激活的路由的状态信息，包含了当前 URL 解析得到的信息，还有 URL 匹配到的路由记录。
路由对象是不可变的，每次成功的导航后都会产生一个新的对象。
// 属性
2.1 path
字符串，对应当前路由的路径，总是解析为绝对路径，如 "/foo/bar"

2.2 params
一个 key/value 对象，包含了动态片段和全匹配片段，如果没有路由参数，就是一个空对象。

2.3 query
一个 key/value 对象，表示 URL 查询参数。例如，对于路径 /foo?user=1，则有 $route.query.user == 1，如果没有查询参数，则是个空对象。

2.4 hash
当前路由的 hash 值 (带 '#') ，如果没有 hash 值，则为空字符串。

2.5 fullPath
完成解析后的 URL，包含查询参数和 hash 的完整路径。

2.6 matched
一个数组，包含当前路由的所有嵌套路径片段的路由记录 。路由记录就是 routes 配置数组中的对象副本 (还有在 children 数组)。

2.7 name
当前路由的名称，如果有的话。

2.8 redirectedFrom
如果存在重定向，即为重定向来源的路由的名字。

2.9 meta
路由元信息，在配置路由时定义

```



## 4.编程式导航

编程式导航可以通过代码直接跳转路由，而无需用户去点击<router-link>标签。主要用到$router的`API`

```js
// 1.push
想要导航到不同的 URL，则使用 router.push 方法。这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，则回到之前的 URL。
router.push('/home')

push还提供 onComplete 和 onAbort 回调作为第二个和第三个参数。这些回调将会在导航成功完成 (在所有的异步钩子被解析之后) 或终止 (导航到相同的路由、或在当前导航完成之前导航到另一个不同的路由) 的时候进行相应的调用。

// 2.replace  
跟push很像，唯一的不同就是，它不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录。

// 3 go(n)
这个方法的参数是一个整数，意思是在 history 记录中向前或者后退多少步

// 4 back
后退

// 5 forward
前进
```



## 5.参数传递

```js
// 1.动态路由传递参数
在定义路由时，使用:进行占位。
示例：
// {path: '/user/:id', componment: User}
// <router-link to='/user/id'>用户</router-link>
在路由组件中通过this.$route.params.id来获取值

// 2.route-link 通过to属性 或 $router.push 以对象为参数值
to属性接受的对象和push、replace规则一样，下边示例以push来演示

$router.push({path: '/user/123',})
// 注意：使用path会把params忽略，所以一般是name配和params来使用
$router.push({name: 'user', params: {id: 123}})

$routerr.push({path: '/user', query: {id: 123}})

前面两种取值：$route.params.id
后面一种取值： $route.query.id
```



## 6.导航守卫

导航守卫主要用来通过跳转或取消的方式守卫导航。有多种机会植入路由导航过程中：全局的, 单个路由独享的, 或者组件级的。 

注意：`params或query`的改变并不会触发进入/离开的导航守卫。你可以通过 `watech $route`响应路由参数的变化，从而来应对这些变化，或使用 `beforeRouteUpdate` 的组件内守卫。 

## 6.1全局守卫

全局守卫的定义需要放在`new Vue`之前，否则不生效

```js
// main.js
import Vue from 'vue'
import App from './App.vue'
import router from './router'

// 跳转前触发，一般用来做数据校验、权限判断
router.beforeEach((to, from, next)=>{
    // to：即将要进入的目标 路由对象
    // from: 当前导航正要离开的路由
    // next: 是一个函数。一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。
    // next(): 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed (确认的)。
    // next(false): 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 from 路由对应的地址。
    // next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。
    // next(error): (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 	   router.onError() 注册过的回调。
    console.log('======router.beforeEach======')
    next()
})

// 和beforeEach没有太多得区别
router.beforeResolve((to, from, next)=>{
    console.log('======router.beforeResolve======')
    next()
})

// 跳转后触发，没有next参数
router.afterEach((to, from)=>{
    console.log('======router.afterEach======')
})

new Vue({
    router,
    render: h => h(App)
}).$mount('#app')
```



## 6.2 路由独享守卫

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Home from '../components/Home/Home'

Vue.use(VueRouter);

const routes = [
    {
        path: '/home',
        component: Home,
        beforeEnter (to, from, next) {
            console.log('======Home beforeEnter======')
            next()
        }
    },
];

export default new VueRouter({
    routes,
})

```



## 6..3 组件内的守卫

```vue
<!--Home.vue-->
<template>
    <div>
        Home
    </div>
</template>

<script>
export default {
    name: 'Home',
    beforeRouteEnter(to, from, next){
        // 在渲染该组件的对应路由被 confirm 前调用
        // 不！能！获取组件实例 `this`
        // 因为当守卫执行前，组件实例还没被创建
        console.log('======Home beforeRouteEnter======')
        next();
    },
    beforeRouteUpdate(to, from, next){
        // 在当前路由改变，但是该组件被复用时调用
        // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
        // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
        // 可以访问组件实例 `this`
        console.log('======Home beforeRouteUpdate======')
        next();
    },
    beforeRouteLeave(to, from, next){
        // 导航离开该组件的对应路由时调用
        // 可以访问组件实例 `this`
        console.log('======Home beforeRouteLeave======')
        next();
    },
}
</script>

<style scoped>

</style>

```



```js
// 执行顺序
// 第一次加载
======router.beforeEach======
======Home beforeEnter======
======Home beforeRouteEnter======
======router.beforeResolve======
======router.afterEach======
    
// 改变/home的params
======router.beforeEach======
======Home beforeRouteUpdate======
======router.beforeResolve======
======router.afterEach======
    
// 跳转到其他路由
======Home beforeRouteLeave======
======router.beforeEach======
======router.beforeResolve======
======router.afterEach======
    

```





## 7.路由过度动画

 `` 是基本的动态组件，所以我们可以用 `` 组件给它添加一些过渡效果 

```jsx
<transition>
  <router-view></router-view>
</transition>
```

### 单个路由的过渡

上面的用法会给所有路由设置一样的过渡效果，如果你想让每个路由组件有各自的过渡效果，可以在各路由组件内使用 `` 并设置不同的 name。 

```vue
const Foo = {
  template: `
    <transition name="slide">
      <div class="foo">...</div>
    </transition>
  `
}

const Bar = {
  template: `
    <transition name="fade">
      <div class="bar">...</div>
    </transition>
  `
}
```

### 基于路由的动态过渡

 还可以基于当前路由与目标路由的变化关系，动态设置过渡效果： 

```html
<!-- 使用动态的 transition name -->
<transition :name="transitionName">
  <router-view></router-view>
</transition>
```

```js
// 接着在父组件内
// watch $route 决定使用哪种过渡
watch: {
  '$route' (to, from) {
    const toDepth = to.path.split('/').length
    const fromDepth = from.path.split('/').length
    this.transitionName = toDepth < fromDepth ? 'slide-right' : 'slide-left'
  }
}
```



## 8. history模式刷新后404问题

```js
1.原因
/*
hash模式下不会出现该问题
刷新 http://127.0.0.1:8080/#/home
实质上请求的是 http://127.0.0.1:8080/  最终返回 index.html
#/home hash部分不会交给服务器
hash部分在我们得到html文件关联的js中作为前台路由路径处理


history模式下，
刷新 http://127.0.0.1:8080/home 
请求路径是 http://127.0.0.1:8080/home，而服务器中没有对该请求的处理，就会出现404问题
*/ 


2.解决
/*
思路： 服务端接受一个无法处理的请求后，返回index.html
express 处理实例如下
const history = require('connect-history-api-fallback');
app.use('/', history());
*/
const port = 3000;
const express = require('express');
const app = express();
const history = require('connect-history-api-fallback');

// 注意: 这句需要放在静态资源管理前面
app.use('/', history());
// 静态资源管理
app.use('/', express.static('./public'));

app.listen(port, ()=>{
	console.log(`server is running in ${port} port`)
})

```

