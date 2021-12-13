# 单组件的生命周期

![](https://cn.vuejs.org/images/lifecycle.png)

## 1.beforeCreate()

### 说明

在实例初始化之后，数据观测（data observer） 和 事件初始化（event/watch）之前被调用。

此时，data、methods、computed、watch、DOM都不能被使用

```js
  data () {
    return {
      msg: 1
    }
  },
  beforeCreate() {
    console.log(this.$data)  //undefined
    console.log(this.$el)    //undefined
  }

```

### 注意

 如果非要在`beforeCreate()`取data也不是没有办法，异步方式可以通过`this.$nextTick()或seTimeout`，

同步方式可以通过`this.$options`。当然一般不会这么操作。 

```js
  beforeCreate() {
    this.$nextTick(() => {       
      console.log(this.msg)                  //1
    })

    setTimeout(()=> {
      console.log(this.msg)                  //1
    }, 1000)

    console.log(this.$options.data()["msg"]) //1
  }

```



## 2.created()

### 说明

在实例创建完成后被立即调用，已完成数据观测和事件初始化。

可以操作data、computed、watch、methods，但DOM还没挂载，不能操作。 

```js
  created () {
    this.getMsg()          //1
    console.log(this.msg)  //1
    console.log(this.$el)  //undefined
  },
  methods: {
    getMsg() {
      console.log(this.msg)
    }
  }

```

 通常在此进行页面初始化操作或简单的Ajax请求（此时页面还未呈现，过多的请求会导致白屏） 



## 3.beforeMount()

### 说明

 在挂载开始之前被调用：相关的 render 函数首次被调用。这一步没啥特别的，很少在此进行操作。 

```js
  beforeMount() {
    console.log(this.msg)  //1
    console.log(this.$el)  //undefined
  }

```



## 4.mounted()

### 说明

 实例被挂载到DOM上。通常用于执行Ajax请求。 

```js
  mounted() {
    console.log(this.msg)  //1
    console.log(this.$el)   //可看到 DOM 的信息
  }

```

### 注意

 mounted 不保证所有子组件都一起被挂载了。若想要等整个视图都渲染完毕再操作，可以使用 `this.$nextTick` 

```js
  mounted() {
    this.$nextTick(()=>{
        
    })
  }

```



## 5.beforeUpdate()

### 说明

 只有数据更新时才调用，发生在虚拟 DOM 打补丁之前。适合在更新之前访问现有的 DOM，比如手动移除已添加的事件 监听器。 

```vue
<div>{{msg}}</div>
```

```js
 mounted() {   
    this.msg = 2
  },
  beforeUpdate: function() {
    console.log(this.$el.innerHTML)  //1
    console.log(this.$el)            //<div ...> 2 </div>
    console.log(this.msg)            //2
  }

```



## 6.updated()

### 说明

 虚拟 DOM 重新渲染和打补丁之后调用，组件DOM已经更新，可以执行依赖于DOM的操作。 

```js
  mounted() {   
    this.msg = 2
  },
  updated: function() {
    console.log(this.$el.innerHTML)  //2
    console.log(this.$el)            //<div ...> 2 </div>
    console.log(this.msg)            //2
  }

```

### 注意

 updated 不保证所有子组件都被重绘了。若想要等到整个视图都重绘完毕再操作，可以用 `this.$nextTick` 



## 7.beforeDestroy()

### 说明

 实例销毁前调用，一般用来清除定时器和事件解绑

```js
  beforeDestroy() {
    console.log(this.msg)  //2
    console.log(this.$el)  //<div ...> 2 </div>
  }

```



## 8.destroyed()

### 说明

 Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。 

```js
 destroyed() {
    console.log(this.msg)  //2
    console.log(this.$el)  //<div ...> 2 </div>
  }

```



## Vue的生命周期可以总结为3个阶段：

1.   初始化阶段（执行1次）：`beforeCreate、created、beforeMount、mounted` 
2.   数据更新阶段（执行多次）：`beforeUpdate、updated` 
3.   销毁阶段/切换组件（执行1次）：`beforeDestory、destoryed` 



# 父子组件的生命周期

## 1.加载渲染过程 

1.  `父beforeCreate`
2.  `父组件created`
3.  `父组件beforeMount`
4.  `子组件beforeCreate`
5.  `子组件create`
6.  `子组件beforeMount`
7.  `子组件mounted`
8.  `父组件mountd`

## 2. 子组件更新过程 

1.  `父组件beforeUpdate`
2.  `子组件beforeUpdate`
3.  `子组件updated`
4.  `父组件updated`

## 3.父组件更新过程 

1.  `父组件beforeUpdate`
2.  `父组件updated`

## 4.销毁过程 

1.  `父组件beforeDestroy`
2.  `子组件beforeDestroy`
3.  `子组件destroyed`
4.  `父组件destroyed`













