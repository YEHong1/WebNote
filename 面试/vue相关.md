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

 vue2采用的是`Object.defineProperty`去定义get，set，而vue3改用了proxy。proxy在 IE 上有兼容性问题

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

模拟`vue`响应式（复杂对象进行递归监听，数组改写原型方法）

```js
function updateView() {
	console.log('=========视图更新===========')
}

// 模拟 vue 中的 data
const data = {
	name: '阿卡丽'
}

// 监听属性值的修改
function definedAttr(obj, key, value){
    // 深度监听（处理复杂对象）
	observer(value)
    
	Object.defineProperty(obj, key,{
		get(){
			return value
		},
		set(newValue){
			if(newValue !== value){
                // 设置新值的时候也要进行深度监听，因为新旧两个值可能会在基本数据类型和引用数据类型间转换
				observer(newValue)
                
				console.log(`修改了 ${key} 属性`)
				value = newValue;
				// 更新视图
				updateView();
			}
		}
	})
}


// `Object.defineProperty`并不能监听数组的变化，那么`vue`是如何监听数组的变化的呢
// 1.获取数组原型
const oldArrPrototype = Array.prototype;
// 2.创建新对象，原型指向 oldArrayProperty ，再扩展新的方法不会影响原型
const arrPrototype = Object.create(oldArrPrototype);
// 3.改写原型里的常用方法，其实就是再原来的基础上加上触发视图更新
['push', 'pop', 'shift', 'unshift', 'splice'].forEach(methodName=>{
	arrPrototype[methodName] = function (){
		// 触发原来的方法
		oldArrPrototype[methodName].call(this, ...arguments)
		// 更新视图
		updateView();
	}
})
// 4.在observer中判断是否是数组，是数组就改变它的隐式原型

// 监听对象属性
function observer(obj) {
	if(typeof obj !== 'object' || obj == null){
		return obj
	}
    
    // 数组在数组方法后面加上触发视图更新的方法
    if(obj instanceof Array){
		obj.__proto__ = arrPrototype;
	}

    // 对象用 Object.defineProperty
	for(let key in obj){
		definedAttr(obj, key, obj[key])
	}
}

observer(data);

data.name = '亚索';
data.name = '永恩';

```



### `Object.defineProperty`的缺点

-   处理复杂对象时，进行深度监听、需要递归到底，一次性计算量大
-   无法监听，新增、删除的属性 （`vue.set、vue.delete`）
-   无法原生监听数组，需要特殊处理



### 为什么可以通过`this.xxx`去改变 `data.xxx`的值

为什么可以通过 `this.xxx` 可以获取和修改 `data.xxx`的内容？

```js
// 其实也还是用到Object.defineProperty
// 遍历 data的数据 ，然后监听this对象，this.xxx的时候返回data.xxx的值，改变this.xxx的值就是改变data.xxx的值

let data = {
	username: '阿卡丽',
	age: 25
}

let _this = {};

for(let key in data){
	Object.defineProperty(_this, key, {
		get(){
			return data[key]
		},
		set(newValue){
			data[key] = newValue
		}
	})
}

console.log(_this.username);
_this.username = '亚索';
console.log(data.username);

```





## 11.虚拟 DOM 和 diff 算法

我们知道频繁地操作 DOM 是非常消耗性能的。 `Vue` 和 `React`是数据驱动视图的框架，随着业务的复杂度提高，用户操作数据的频率就越高，它们是如何处理 DOM渲染的呢？答案就是 虚拟DOM

JavaScript的运行速度是比较快的，react 提出了 把DOM计算更多地转换为 JavaScript计算。于是提出了虚拟DOM

`VDOM`：用JavaScript模拟DOM结构，计算出最小的变更，再去操作DOM

### 用JavaScript模拟DOM结构

```html
<div id="div1" class="container">
    <p>Hello World</p>
    <ul style="font-size: 18px">
        <li>列表项1</li>
    </ul>
</div>
```

转换为`vdom`对象

```js
let vdom = {
	tag: 'div',
	props: {
		id: 'div1',
		className: 'container'
	},
	children: [
		{
			tag: 'p',
			children: 'Hello World'
		},
		{
			tag: 'ul',
			props: {
				style: 'font-size: 20px'
			},
			children: [
				{
					tag: 'li',
					children: '列表项1'
				}
			]
		}
	]
}

```



### diff 算法

-   diff算法就是用来计算出DOM的最小变更，是 `vdom`中最核心、最关键的部分
-   diff算法能在日常使用 `vue react`中体现出来 （如`key`）

通过比较新旧DOM树，要遍历所有的DOM结构的话，是比较复杂的，难以计算，所以提出了：

-   只比较同一层级，不跨级比较
-   `tag`不相同，直接删掉重建，不再深度比较
-   `tag 和 key`，两者都相同，则认为是相同节点，不再深度比较



## 11.`v-if` 与 `v-show`的区别

-   v-show 通过 CSS 的 display控制显示和隐藏
-   v-if 组件真正的渲染和销毁，而不是显示和隐藏
-   频繁切换用v-show， 否则用v-if



## 12. 为何在v-for中使用key

-   必须使用key， 且不能是 index 和 random
-   diff 算法中通过 tag（标签） 和 key 来判断，是否是相同的节点 (`sameNode`)
-   减少渲染次数，提高渲染性能



 `vue和react`都是采用diff算法来对比新旧节点，从而更新DOM。 diff 算法中通过 tag（标签） 和 key 来判断，是否是相同的节点 (`sameNode`)。

举个例子：

一个数组的值是 【A, B, C, D】，被改变为 【B, C, D, A】

在没有key的情况下：

diff算法判断出存在差异，直接销毁重建节点。

在有key的情况下：

1.对新集合的节点进行遍历，通过唯一 key 可以判断新老集合中是否存在相同节点。
2.如果存在相同节点，则进行移动操作，但在移动前，需要将当前节点在老集合中的位置与 `lastIndex` 进行比较，如果不同，则进行节点移动，否则不执行该操作。



### 为什么key不能是 index 或random？

diff算法是比较同级之间的不同并以key值来进行关联。当对数组进行下标的变换时，比如删除第一条数据，那么以后所有的Index都会发生改变，那么key值自然也跟着全部发生改变 



## 13.v-for 和 v-if 不要在同一个标签中使用

`v-for` 的执行优先级比 `v-if`高，每遍历一次之后都需要判断 `v-if`，性能消耗比较大。建议将 `v-if`放到 `v-for`的上层标签中



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



## 16.为何组件中的data必须是一个函数

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



## `Vue`模板的编译过程

```js
// 1.使用 vue-template-compiler 解析template 为 render函数
// 2.render函数生成 虚拟DOM
// 3.再根据虚拟DOM渲染成真实DOM
```



## 组件渲染和更新过程

```js
// 渲染过程
1.解析模板为 render 函数
2.触发响应式，监听 data 数据的getter 和 setter
3.执行render函数，生成虚拟DOM，并最终渲染成真实DOM

// 更新过程
1.修改 data 中的数据，触发 setter
2.重新执行 render函数，生成新的虚拟DOM
```



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





