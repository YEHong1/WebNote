## 1.1 `VueX`是什么

`VueX`是一个专门为 vue.js 应用程序开发的**状态管理模式**， 它采用**集中式存储**管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化 。



## 1.2 什么是状态管理模式

状态管理模式由三部分组成：

-   state（数据源）
-   view（视图）
-   action（用户操作）

视图的内容依赖于数据源，数据源发生改变时页面也会随之更改。

用户在视图上的操作可以改变数据源，从而改变视图内容。这构成了单向数据流。

**单向数据流概念图**

![flow](.\images\flow.png)



## 1.3多组件共享数据问题

当应用有多个组件共享状态时，单向数据流的简洁性很容易被破坏：

-   多个视图依赖于同一状态
-   来自不同视图的行为需要改变同一状态

 对于问题一，传参的方法对于多层嵌套的组件将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。 

 对于问题二，我们经常会采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。

以上的这些模式非常脆弱，通常会导致无法维护的代码。 

因此，我们为什么不把组件的共享状态抽取出来，以一个全局单例模式管理呢？在这种模式下，我们的组件树构成了一个巨大的“视图”，不管在树的哪个位置，任何组件都能获取状态或者触发行为！

通过定义和隔离状态管理中的各种概念并通过强制规则维持视图和状态间的独立性，我们的代码将会变得更结构化且易维护。





## 2.1 `VueX`核心概念

`VueX`应用的核心就是store，它就相当于一个容器，包含了我们应用中大部分的状态（state）。

`VueX`和单纯的全局对象的不同之处在于：

-    `Vuex` 的状态存储是响应式的。当 `Vue` 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新（利用了组件的**计算属性**）。 
-    你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地**提交 (commit) mutation**。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。 



![](./images/vuex.png)



Actions不能直接修改state的数据，而是通过commit方法来触发Mutations中的函数来修改state。修改state后页面重新渲染



## 2.2  核心API

## 2.2.1 Store对象

```js
// 属性
state,      // 状态对象
getters,    // 包含多个getter计算属性的对象
mutations,  // 包含多个更新state函数的对象
actions,    // 包含多个对应事件回调函数的对象
modules		// 将 store 分割成模块,每个模块拥有自己的 state、mutation、action、getter、modules

// 方法
dispatch( actionName, data )
// actionName 是 store.actions 对象中对应函数的名称
// data 为需要传递的数据

commit( type )
// type 为 mutations中某个函数的名称
```

创建一个Store对象

```js
// store.js
import Vue from 'vue';
import Vuex from 'vuex';

// 声明使用vuex
Vue.use(Vuex);

let state = {};
let getters = {};
let mutations = {};
let actions = {};

//向外暴露store对象
export default new Vuex.Store({
    state,      // 状态对象
    getters,    // 包含多个getter计算属性的对象
    mutations,  // 包含多个更新state函数的对象
    actions     // 包含多个对应事件回调函数的对象
});
```



```js
// main.js
import Vue from 'vue'
import App from './App'
import store from "./store";

// 映射store后，所有组件都会多出一个属性：$store，这样可以让我们减少在组件中引入store的次数
new Vue({
    el: '#app',
    store,
    components: {App},
    template: '<App/>'
});

```





## 2.2.2 State

状态对象

```js
// state.js
const state = {
    // 全局
    count: 0
}

export default state;
```

```js
// 在组件中获取count的值
$store.state.count

// 监听count的更新
computed: {
    count () {
        return this.$store.state.count
    }
}
```

```js
// 当一个组件需要获取多个状态的时候，将这些状态都声明为计算属性会有些重复和冗余。
// 为了解决这个问题，我们可以使用 mapState 辅助函数帮助我们生成计算属性
import { mapState } from 'vuex'

// 写法1
computed: mapState({
    count: state=> state.count
})

// mapState返回对象
// count: 'count' 在mapState中等价于 count: state=> state.count
// 所以写法1可简写为：
computed: {
    ...mapState(['count'])
}

// 如果需要去别名，可以这样写：
computed: {
    ...mapState({
        num: 'count'
    })
}
```



## 2.2.3 Getter

 `Vuex` 允许我们在 store 中定义 getter（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。 

```js
// getter对象里存放函数，函数的参数为 state 和 getter
const getters = {
    getLoveSingerList(state, getters){
        return []
    }
};
export default getters;
```

```js
// 在组件中获取对应的getter值

// 1.通过方法去调用
// 注意，getter 在通过方法访问时，每次都会去进行调用，而不会缓存结果。
console.log(this.$store.getters.getLoveSingerList);

// 2.通过 mapGetters 辅助函数映射到局部计算属性
import { mapGetters } from 'vuex'

computed: {
    ...mapGetters([
        'getLoveSingerList'
    ])
}
```



##  2.2.4 Mutation

更改 `Vuex` 的 store 中的状态的唯一方法是提交 mutation。

`Vuex` 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 **事件类型 (type)** 和 一个 **回调函数 (handler)**。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数

```js
const store = new Vuex.Store({
    state: {
        count: 1
    },
    mutations: {
        // 第一个参数为 state， 第二个参数为commit时传递过来的数据
        increment (state, num) {
            // 变更状态
            state.count += num
        }
    }
})
```

 你不能直接调用一个 mutation handler。这个选项更像是事件注册：“当触发一个类型为 `increment` 的 mutation 时，调用此函数。”要唤醒一个 mutation handler，你需要以相应的 type 调用 `store.commit` 方法： 

```js
store.commit('increment', 10)
```



### Mutation 需遵守 `Vue` 的响应规则

既然 `Vuex` 的 store 中的状态是响应式的，那么当我们变更状态时，监视状态的 `Vue` 组件也会自动更新。这也意味着 `Vuex` 中的 mutation 也需要与使用 `Vue` 一样遵守一些注意事项： 

-    最好提前在你的 store 中初始化好所有所需属性 

-    当需要在对象上添加新属性时 使用 

    `Vue.$set(obj, newProp, value)` 或  以新对象替换老对象 `obj = {...obj, newProp: 123}`



### 使用常量替代 Mutation 事件类型

使用常量替代 mutation 事件类型在各种 Flux 实现中是很常见的模式。这样可以使 linter 之类的工具发挥作用，同时把这些常量放在单独的文件中可以让你的代码合作者对整个 app 包含的 mutation 一目了然 

```js
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'
```

```js
// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
    state: { ... },
    mutations: {
        // 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
        [SOME_MUTATION] (state) {
            // mutate state
        }
    }
})
```



### Mutation 必须是同步函数

现在想象，我们正在 debug 一个 app 并且观察 `devtool` 中的 mutation 日志。每一条 mutation 被记录，`devtools` 都需要捕捉到前一状态和后一状态的快照。然而，在上面的例子中 mutation 中的异步函数中的回调让这不可能完成：因为当 mutation 触发的时候，回调函数还没有被调用，`devtools` 不知道什么时候回调函数实际上被调用——实质上任何在回调函数中进行的状态的改变都是不可追踪的。 

所以我们一般在Action中完成异步任务后再commit mutation



### 在组件中提交 Mutation

```js
// 法1 
this.$store.commit('xxx')

// 法2 使用 mapMutations 辅助函数
import { mapMutations } from 'vuex'

export default {
    // ...
    methods: {
        ...mapMutations([
            // 将 `this.increment()` 映射为 `this.$store.commit('increment')`
            'increment', 
            // 将this.incrementBy(amount)映射为this.$store.commit('incrementBy', amount)
            'incrementBy'
        ]),
        
        ...mapMutations({
            // 将 `this.add()` 映射为 `this.$store.commit('increment')`
            add: 'increment' 
        })
    }
}

```





## 2.2.5 Action

 Action 类似于 mutation，不同在于： 

-   Action 提交的是 mutation，而不是直接变更状态。
-   Action 可以包含任意异步操作。

Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 `context.commit` 提交一个 mutation，或者通过 `context.state` 和 `context.getters` 来获取 state 和 getters。 

```js
const store = new Vuex.Store({
    state: {
        count: 0
    },
    mutations: {
        increment (state) {
            state.count++
        }
    },
    actions: {
        // context 不是 store本身，这是包含了和store一样的数据
        increment ({ commit }, data) {
            commit('increment')
        }
    }
})

```



### 在组件中分发 Action

```js
// 法1
this.$store.dispatch('increment', data)

// 法2
import { mapActions } from 'vuex'

export default {
    methods: {
        ...mapActions([
            // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`
            'increment',
            // 将this.incrementBy(amount)映射为this.$store.dispatch('incrementBy', amount)
            'incrementBy' 
        ]),
        ...mapActions({
            add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
        })
    }
}
```



### 组合 Action

Action 通常是异步的，那么如何知道 action 什么时候结束呢？更重要的是，我们如何才能组合多个 action，以处理更加复杂的异步流程？ 

首先，你需要明白 `store.dispatch` 可以处理被触发的 action 的处理函数返回的 Promise，并且 `store.dispatch` 仍旧返回 Promise： 

```js
actions: {
    actionA ({ commit }) {
        // action处理函数返回一个promise
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                commit('someMutation')
                resolve()
            }, 1000)
        })
    }
}


store.dispatch('actionA').then(()=>{
    // 在dispatch action 完成后执行的逻辑
})
```



## 2.2.6 Module （用得少，暂不做笔记）

当项目比较复杂，需要管理的数据很多时，store容易变得大而杂。module就是用来解决这个问题的。

`Vuex` 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块 

```js
const moduleA = {
    state: () => ({ ... }),
    mutations: { ... },
    actions: { ... },
    getters: { ... }
}

const moduleB = {
    state: () => ({ ... }),
    mutations: { ... },
    actions: { ... }
}

const store = new Vuex.Store({
    modules: {
        a: moduleA,
        b: moduleB
    }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```



