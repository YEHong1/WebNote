### 1.1.1路由介绍



### 1.1.2基本路由

![1581087549504](images\1581087549504.png)



```html
//router-link类似与a标签，to属性表示切换到指定的路由
<router-link to='/home'>Home</router-link>
<router-link to='/about'>Home</router-link>

//router-view用来显示当前路由对应的组件
<router-view/>
```

需要注意的是当用户点击router-link时，改标签会添加一个class的值：router-link-active。

我们可以根据该特性修改css，特殊显示被选中的router-link标签的样式。

