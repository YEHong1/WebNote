## 1.1.1 `VueX`是什么

`VueX`是一个专门为 vue.js 应用程序开发的**状态管理模式**， 它采用**集中式存储**管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化 。



## 1.1.2 什么是状态管理模式

状态管理模式由三部分组成：

state		驱动应用的数据源

view		以声明的方式将state映射到视图中

actions	响应在view上的用户操作导致的状态变化



以一个计数应用来讲解

```js
new Vue({
  // state
  data () {
    return {
      count: 0
    }
  },
  // view
  template: `
    <div>{{ count }}</div>
  `,
  // actions
  methods: {
    increment () {
      this.count++
    }
  }
})
```

用户通过操作改变了count这个数据，从而导致页面发生改变



**单向数据流概念**

![flow](.\images\flow.png)



## 1.1.3多组件共享数据问题

当应用有多个组件共享状态时，单向数据流的简洁性很容易被破坏：

-   多个视图依赖于同一状态
-   来自不同视图的行为需要改变同一状态

 对于问题一，传参的方法对于多层嵌套的组件将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。 

 对于问题二，我们经常会采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。

以上的这些模式非常脆弱，通常会导致无法维护的代码。 

因此，我们为什么不把组件的共享状态抽取出来，以一个全局单例模式管理呢？在这种模式下，我们的组件树构成了一个巨大的“视图”，不管在树的哪个位置，任何组件都能获取状态或者触发行为！

通过定义和隔离状态管理中的各种概念并通过强制规则维持视图和状态间的独立性，我们的代码将会变得更结构化且易维护。





## 2.1.1 `VueX`核心概念和API

`VueX`应用的核心就是store，它就相当于一个容器，包含了我们应用中大部分的状态（state）。

`VueX`和单纯的全局对象的不同之处在于：

-    `Vuex` 的状态存储是响应式的。当 `Vue` 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新（利用了组件的**计算属性**）。 
-    你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地**提交 (commit) mutation**。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。 







![](./images/vuex.png)



Actions不能直接修改state的数据，而是通过commit方法来触发Mutations中的函数来修改state。修改state后页面重新渲染



## 2.1.2 Store对象

Store对象包含： state、getter、mutations、actions、modules

创建一个Store对象

store.js

```js
//引入包
import Vue from 'vue';
import Vuex from 'vuex';

//声明使用vuex
Vue.use(Vuex);

let state = {};
let getters = {};
let mutations = {};
let actions = {};

//向外暴露store对象
export default new Vuex.Store({
    state,      //状态对象
    getters,    //包含多个getter计算属性的对象
    mutations,  //包含多个更新state函数的对象
    actions     //包含多个对应事件回调函数的对象
});
```



main.js

```js
import Vue from 'vue'
import App from './App'
import store from "./store";

//映射store后，所有组件都会多出一个属性：$store
new Vue({
    el: '#app',
    store,
    components: {App},
    template: '<App/>'
});

```



所有用`Vuex`管理的组件中多了一个属性：$store， 它就是一个store对象

属性：

​	state： 注册的state对象

​	getters: 	注册的getters对象

方法：

​	dispatch( `actionName`, `data` );

​	`actionName` 是 `store.actions` 对象中对应函数的名称

​	`data` 为需要传递的数据



使用例子

`App.vue`

```js
<template>
    <div id="app">
        <p>点击了{{$store.state.count}}次，当前次数为{{evenOrOdd}}</p>
        <button @click="increment">increment</button>
    </div>
</template>

<script>

    export default {
        name: 'App',
        computed: {
            evenOrOdd() {
                return this.$store.getters.oddOrEven;
            }
        },
        methods: {
            increment(){
                //view 通过 dispatch 触发 actions中的 increment函数
                this.$store.dispatch('increment');
            },
        }
    }
</script>

<style>

</style>


//下面代码为上面代码的简写=====================================================
<template>
    <div id="app">
        <p>点击了{{$store.state.count}}次，当前次数为{{evenOrOdd}}</p>
        <button @click="increment">increment</button>
    </div>
</template>

<script>
    import {mapState, mapGetters, mapActions, mapMutations} from 'vuex';
    export default {
        name: 'App',
        computed: {
            // evenOrOdd() {
            //     return this.$store.getters.oddOrEven;
            // },
            ...mapGetters({evenOrOdd: 'evenOrOdd'}),
            // mapGetters: (name)=>{return this.$store.getters[name]}
            
            // count(){
            //     return this.$store.state.count;
            // }
            ...mapState[{count: 'count'}]
            //mapState: (name)=>{ return this.$store.state[name]}
        },
        methods: {
            // increment(){
            //     //view 通过 dispatch 触发 actions中的 increment函数
            //     this.$store.dispatch('increment');
            // },
            ...mapActions(['increment'])
            // mapActions: function (name) {
            //     this.$store.dispatch(name);
            // }
        }
    }
</script>

<style>

</style>

```

main.js

```js
import Vue from 'vue'
import App from './App'
import store from "./store";

new Vue({
    el: '#app',
    store,
    components: {App},
    template: '<App/>'
});

```

store.js

```js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

let state = {
    count: 0
};

let getters = {
    //state 和 getter为自带的参数，不用我们去传参
    oddOrEven(state, getter){
        return state.count%2 === 0 ? '偶数' : '奇数';
    }
};

//只有mutations中方法才可以直接修改state
let mutations = {
    AddCount(state){
        state.count++;
    },
};

let actions = {
    increment({commit}){
        //commit方法去触发mutations中的 AddCount方法
        commit('AddCount');
    },
};

export default new Vuex.Store({
    state,      //状态对象
    getters,    //包含多个getter计算属性的对象
    mutations,  //包含多个更新state函数的对象
    actions     //包含多个对应事件回调函数的对象
});

```



