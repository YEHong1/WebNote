## 1.兄弟组件通信

方法一：子组件A通过自定义事件传值给父组件P，然后父组件P传值给子组件B

方法二： `eventBus` 实现A传值给B

```js
//1.创建一个js文件，eventBus.js，位置随便放
import Vue from 'vue'
export default new Vue()
```

```js
//2.在兄弟组件A中引入eventBus.js
import eventBus from './eventBus'

//在methods里边定义一个函数， 需要传值时调用这个方法
methods:{
    changesize(){
        eventBus.$emit('add',this.arg)
    }
}


```

```js
//3.在兄弟组件B中
//在created生命周期函数中定义一个自定义事件 add
created(){
    eventBus.$on('add',(message)=>{
        //一些操作，message就是从A组件传过来的值
        console.log(message)
    })
},
```



## 2.beforDestore中一般进行什么操作

```js
// 1.清除定时器
// 2.事件解绑
// 这样做是为了防止内存泄漏
```



## 3.vue组件更新后如何获取最新的DOM

-   `Vue`是异步渲染的

-   data改变后，DOM不会立刻渲染

-   `$nextTick`会在DOM渲染后被触发，以获取最新的DOM

    

    ```js
    // 修改数据
    vm.msg = 'Hello'
    // DOM 还没有更新
    Vue.nextTick(function () {
      // DOM 更新了
    })
    
    // 作为一个 Promise 使用 (2.1.0 起新增，详见接下来的提示)
    Vue.nextTick()
      .then(function () {
        // DOM 更新了
      })
    ```

    

## 4.slot的使用

```js
//  https://segmentfault.com/a/1190000020800845?utm_source=tag-newest
```



## 5.动态组件

```vue
<!-- 基本使用-->
<component :is="componentName"></component>

<!--场景： 点击tab切换不同的组件-->

<!--可以配合keep-alive使用，来让组件在切换时缓存之前的状态，而不是重新渲染-->
<!-- 失活的组件将会被缓存！-->
<keep-alive>
  <component v-bind:is="currentTabComponent"></component>
</keep-alive>

<!--注意这个 <keep-alive> 要求被切换到的组件都有自己的名字，不论是通过组件的 name 选项还是局部/全局注册。-->
```



## 6.异步组件

```js
/**
使用场景：
我有一个体积比较大的组件，并且他在页面渲染时不是直接显示的，而是满足某些条件后才显示
假设现在我有一个BigComponment组件
**/

// 同步组件写法
import BigComponment from './BigComponent'
new Vue({
  components: {
    BigComponment
  }
})

// 异步组件写法， 不直接import, 而是使用import()函数
new Vue({
  components: {
    BigComponment: () => {
        return import('./BigComponent')
    }
  }
})

// 当用户点击按钮满足条件后才去import BigComponent

// 使用异步组件的好处在于：减少了打包后的体积，加快应用的加载速度


```



## 7.`vue`如何缓存组件

```vue
<!--
vue中组件的缓存可以使用 keep-alive
场景： 频繁切换，但不需要重新渲染
是性能优化的一种方法

有时候也可以使用v-show来代替
两者的不同在于 v-show是通过css属性的display:none来控制的，适合简单情况
keep-alive则是vue层级或者说是js来控制的，适合复杂情况，比如tab栏切换
-->

<keep-alive>
    <ComponentA/>
    <ComponentB/>
</keep-alive>
```



## 8.minxin

-   多个组件有相同的逻辑，需要抽离出来
-   `minxin`并不是完美的解决方案，会有一些问题
-   `Vue3`提出的Composition API 旨在解决这些问题



```js
var minxin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('from mixin')
    }
  }
}

var vm = new Vue({
  mixins: [minxin],
  methods: {
    bar: function () {
      console.log('bar')
    },
    conflicting: function () {
      console.log('from self')
    }
  }
})

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "from self"
```



## 9.如何理解MVVM

MVVM 数据驱动视图

更新视图不直接操作DOM，而是通过修改数据来驱动视图的更新



1.首先M代表数据模型，V代表视图，VM(即`ViewModel`)是连接数据和视图的一个桥梁

2.view通过事件绑定来操作model，同时model通过数据绑定来操作view

总结：`mvvm`视图和数据是分离的，他们通过中间桥梁`viewmodel`实现视图的更新 





## 10.`Vue`响应式

-   组件 data 的数据一旦发生改变，立刻触发视图的更新
-   这是实现数据驱动视图的第一步

 vue2采用的是`defineProperty`去定义get，set，而vue3改用了proxy。proxy在 IE 上有兼容性问题

```js
const obj = {};
let name = '123'

// Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。
Object.defineProperty(obj, 'name', {
    get() {
        console.log('获取name的值')
        return name
    },
    set(newVal) {
        console.log('设置name的值');
        name = newVal
    }
})

console.log(obj.name); // 打印 获取name的值
obj.name = '321' // 打印 设置name的值
```



## 11.`v-if` 与 `v-show`的区别

-   v-show 通过 CSS 的 display控制显示和隐藏
-   v-if 组件真正的渲染和销毁，而不是显示和隐藏
-   频繁切换用v-show， 否则用v-if



## 12. 为何在v-for中使用key

-   必须使用key， 且不能是 index 和 random
-   diff 算法中通过 tag（标签） 和 key 来判断，是否是相同的节点 (`sameNode`)
-   减少渲染次数，提高渲染性能



## 13.描述`Vue`组件声明周期(父子组件)

1.  加载渲染过程

    ```js
    // 1.父beforeCreate
    // 2.父created
    // 3.父beforeMount
    // 4.子beforeCreate
    // 5.子created
    // 6.子beforeMounted
    // 7.子mounted
    // 8.父mounted
    ```

2.   子组件更新过程 

    ```js
    // 1.父beforeUpdate
    // 2.子beforeUpdate
    // 3.子updated
    // 4.父updated
    ```

3.   父组件更新过程 

    ```js
    // 父beforeUpdate => 父updated
    ```

4.   销毁过程 

    ```js
    // 1.父beforeDestroy
    // 2.子beforeDestroy
    // 3.子destroyed
    // 4.父destroyed
    ```

    



## 14.Vue组件常见的通讯方式

-   父子组件通过 props 和 this.$emit
-   自定义事件 event.$on、event.$off、 event.$emit
-   `vuex`



## 15.computed有什么特点

-   存在缓存，data中的数据不发生改变，不会重新计算
-   合理的使用computed可以提高程序的性能



### 16.为何组件中的data必须是一个函数

-   组件中data值不能为对象，因为对象是引用类型，组件可能会被多个实例同时引用。如果data值为对象，将导致多个实例共享一个对象，其中一个组件改变data属性值，其它实例也会受到影响。

-   上面解释了data不能为对象的原因，这里我们简单说下data为函数的原因。data为函数，通过return 返回对象的拷贝，致使每个实例都有自己独立的对象，实例之间可以互不影响的改变data属性值。



## 17.ajax请求应该放在那个生命周期

-   应该放在mounted生命周期
-   `js`是单线程的，ajax是异步的，在Mounted之前调用ajax只会让逻辑看起来更混乱



## 18.如何将组件的所有props传递给子组件

```js
// 可以使用$props
<user v-bind='$props'/>
```



## 19. 如何实现自己的v-model

```vue
<!--
1.定义一个MyInput组件， 该组件接受传过来的属性name，并以传过来的属性name作为input的value
2.监听输入框的input事件，在以MyInput组件为子组件的App组件处，自定义事件changeName，将接受到的值赋值给对应的data中的属性
-->
<template>
	<div>
        <input type="text" :value="name" @input="changeName">
	</div>
</template>

<script>
    export default {
        props: ['name'],
        name: "MyInput",
        data(){
            return {}
        },
        methods: {
            changeName(e){
                this.$emit('changeName', e.target.value)
            }
        }
    }
</script>

<style scoped>

</style>


<!--App组件-->
<template>
    <div id="app">
        <MyInput type="text" :name="name" @changeName="changeName"/>
        {{name}}
    </div>
</template>

<script>
    import MyInput from "./components/MyInput";
    export default {
        name: 'App',
        data() {
            return {
                name: ''
            }
        },
        components: {
            MyInput
        },
        methods: {
            changeName(name){
                this.name = name
            },
        }
    }
</script>

<style>

</style>


```



## 20.什么时候使用异步组件

-   加载大组件
-   路由懒加载



## 21.什么时候使用keep-alive

-   缓存组件，不需要重复渲染



## 22.Vuex中action和mutation有何区别

-   action中可以处理异步操作，而mutation不可以
-   mutation中做一些原子性操作
-   action可以整合多个mutation

## 23.Vue-Router中的路由模式

-   hash模式 (默认)
-   H5 history模式（需要服务端支持）



## 24.Vue如何监听数组的变化

-   `Object.defineProperty`不能监听数组的变化
-   重新定义原型，重写push pop等方法，实现监听
-   Proxy可以原生支持监听数组变化，但是他有兼容性问题



## 25.什么是组件

-   组件是可复用的`vue`实例
-   每一个组件都是单独的功能模块
-   组件的本质是产生虚拟DOM
-   使用组件可以提高代码的复用性和可维护性



## 26.Vue为何是异步渲染，$`nextTick`有何用

-   异步渲染可以合并data的修改，从而提高渲染性能

-   $`nextTick`在DOM更新完后触发回调

    

## 27.Vue常见的性能优化

-   合理使用v-show和v-if， v-if的性能开销更大
-   合理使用computed，computed有缓存，data不发生改变，不会重新计算
-   使用v-for时加key，以及避免和v-if同时使用，因为v-for的优先级更高，执行完v-for后再判断是否需要渲染
-   自定义事件、DOM事件，定时器需要及时销毁，否则可能产生内存泄漏
-   合理使用异步组件
-   合理使用keep-alive
-   data层级不要太深，这个涉及到了双向数据绑定





