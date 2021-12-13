# React中使用CSS样式



## 1.行内样式

-    style 接受一个采用小驼峰命名属性的 JavaScript 对象，而不是 CSS 字符串；  
-    并且可以引用state中的状态来设置相关的样式； 

优点：

-    内联样式, 样式之间不会有冲突  
-    .可以动态获取当前state中的状态 

缺点：

-    大量的样式, 代码混乱 
-    某些样式无法编写(比如伪类/伪元素) 

```jsx
class App extends React.Component {
  render() {
    return (
      <div style={{ background: '#eee', width: '200px', height: '200px'}}>
        <p style= {{color:'red', fontSize:'40px'}}>行内样式</p>
      </div>
    )
  }
}
```





## 2. **引入样式** 

 引入样式就是将CSS文件写下外部，再引入使用，这种普通的引入样式实际上会存在一些问题 

-    CSS的规则都是全局的，任何一个组件的样式规则，都对整个页面有效，这可能会导致大量的冲突 
-    在组件化开发中我们总是希望组件是一个独立的模块，即便是样式也只是在自己内部生效，不会相互影响 
-   简单的解决办法就是将样式的命名变得复杂且不重复，但这样样式多了也很难避免重复，且命名也不会太好看

```jsx
import React, {Component} from 'react';
// 外部定义的css文件
import './app.css';

export default class App extends Component {

    render() {
        return (
            <p>App</p>
        )
    }
}
```



## 3.CSS Modules (推荐)

CSS Modules 的做法就是通过配置将`css`文件进行编译，编译后在每个用到`css`的组件中的`css`类名都是独一无二的，从而实现CSS的局部作用域。 

 create-react-app2.0.版本后就已经开始支持CSS Modules了，不需要自己去配置`webpack`

```css
/*App.module.css*/
.title{
    color: deepskyblue;
    font-size: 26px;
}
```



```jsx
// 1. 将App.css改名为App.module.css;
// 2. import AppStyle from './App.modules.css'; 而不是 import './App.modules.css';
// 3. <p className={AppStyle.类名}>App</p>

import React, {Component} from 'react';
import AppStyle from './AppStyle.module.css';

export default class App extends Component {

    render() {
        return (
            <p className={AppStyle.title}>App</p>
        )
    }
}
```



