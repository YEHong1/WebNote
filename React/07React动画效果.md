# 在React中实现动画效果

react-transition-group是React官方推荐使用的一个动画库

```shell
npm i react-transition-group

文档地址
https://reactcommunity.org/react-transition-group/
```



## react-transition-group主要包含四个组件：  

###  Transition 

-    该组件是一个和平台无关的组件（不一定要结合CSS）

-    在前端开发中，我们一般是结合CSS来完成样式，所以比较常用的是`CSSTransition`;

​    

###  `CSSTransition` 

-    在前端开发中，通常使用`CSSTransition`来完成过渡动画效果 
-    `CSSTransition`是基于Transition组件构建的 
-    `CSSTransition`执行过程中，有三个状态：appear、enter、exit；  

```js
// in：触发进入或者退出状态
1.如果添加了unmountOnExit={true}，那么该组件会在执行退出动画结束后被移除掉
2.当in为true时，触发进入状态，会添加-enter、-enter-acitve的class开始执行动画，当动画执行结束后，会移除两个class，并且添加-enter-done的class
3.当in为false时，触发退出状态，会添加-exit、-exit-active的class开始执行动画，当动画执行结束后，会移除两个class，并且添加-enter-done的class

// classNames：动画class的名称
1.决定了在编写css时，对应的class名称，如下
classsNames={fade} fade-enter、fade-enter-active、fade-enter-done

// timeout：过渡动画的时间

// appear: 是否在初次进入添加动画（需要和in同时为true）

// unmountOnExit：退出后卸载组件

// 还有一些对应的钩子函数：主要为了检测动画的执行过程，来完成一些JavaScript的操作
1.onEnter：在进入动画之前被触发
2.onEntering：在应用进入动画时被触发
3.onEntered：在应用进入动画结束后被触发

```



```css
/*App.css*/
.card-enter,.card-appear{
    opacity: 0;
    transform:scale(.4);
}
.card-enter-active,.card-appear-active{
    opacity: 1;
    transform: scale(1);
    transition: opacity 600ms,transform 600ms;
}
.card-exit{
    opacity: 1;
    transform: scale(1);
}
.card-exit-active{
    opacity: 0;
    transform: scale(.4);
    transition: opacity 600ms,transform 600ms;
}
.card-exit-done{
    opacity: 0;
}
```



```jsx
// App.js
import React, {Component} from 'react';
import {CSSTransition} from 'react-transition-group';
import './App.css';

export default class App extends Component {
    constructor(props) {
        super(props);
        this.state = {
            isShow: true
        }
    }

    render() {
        const {isShow} = this.state;
        return (
            <>
                <button onClick={this.changeShowStatus}>切换显示状态</button>
                {/*定义过度动画的样式需要在全局css中定义，如果是在cssModules中定义则不会生效*/}
                <CSSTransition
                    in={isShow}
                    classNames={'card'}
                    timeout={600}
                    appear
                >
                    <div style={{width: 360, height: 150, backgroundColor: 'pink'}}/>
                </CSSTransition>
            </>
        )
    }

    changeShowStatus = ()=>{
        const {isShow} = this.state;
        this.setState({
            isShow: !isShow
        })
    }

}
```



###  `SwitchTransition`

-    可以完成两个组件之间切换的炫酷动画 
-    这个动画的灵感来源于 `vue transition modes`

```js
// mode 模式
1.in-out：表示新组件先进入，旧组件再移除
2.out-in：表示就旧组件先移除，新组件再进入

// 使用 SwitchTransition 需要注意两个点
1.SwitchTransition组件里面要有CSSTransition或者Transition组件，不能直接包裹你想要切换的组件
2.SwitchTransition里面的CSSTransition或Transition组件不再像以前那样接受in属性来判断元素是何种状态，
  取而代之的是key属性
  
```



```css
/*App.css*/
.card-enter,.card-appear{
    opacity: 0;
    transform:scale(.4);
}
.card-enter-active,.card-appear-active{
    opacity: 1;
    transform: scale(1);
    transition: opacity 600ms,transform 600ms;
}
.card-exit{
    opacity: 1;
    transform: scale(1);
}
.card-exit-active{
    opacity: 0;
    transform: scale(.4);
    transition: opacity 600ms,transform 600ms;
}
.card-exit-done{
    opacity: 0;
}
```



```jsx
import React, {Component} from 'react';
import {CSSTransition, SwitchTransition} from 'react-transition-group';
import './App.css';

export default class App extends Component {
    constructor(props) {
        super(props);
        this.state = {
            isShow: true
        }
    }

    render() {
        const {isShow} = this.state;
        return (
            <>
                <button onClick={this.changeShowStatus}>更换组件</button>
                <SwitchTransition mode={'out-in'}>
                    <CSSTransition
                        key={isShow}
                        classNames={'card'}
                        timeout={600}
                        appear
                    >
                        {
                            isShow ? (
                                /*组件A*/
                                <div style={{width: 360, height: 150, backgroundColor: 'pink'}}/>
                            ): (
                                /*组件B*/
                                <div style={{width: 360, height: 150, backgroundColor: 'blue'}}/>
                            )
                        }
                    </CSSTransition>
                </SwitchTransition>
            </>
        )
    }

    changeShowStatus = ()=>{
        const {isShow} = this.state;
        this.setState({
            isShow: !isShow
        })
    }

}
```



###  `TransitionGroup` 

-    当我们有一组动画时，需要将这些`CSSTransition`放入到一个`TransitionGroup`中来完成动画 
-   一般是在列表中使用`TransitionGroup` 

```css
/*App.css*/
.card-enter{
    opacity: 0;
}

.card-enter-active{
    opacity: 1;
    transition: all 600ms;
}

.card-enter-done{
    color: pink;
}

.card-exit{
    opacity: 1;
}

.card-exit-active{
    opacity: 0;
    transition: all 600ms;
}

.card-exit-done{
    opacity: 0;
}
```

```jsx
import React, {Component} from 'react';
import {CSSTransition, TransitionGroup} from 'react-transition-group';
import './App.css';

export default class App extends Component {
    constructor(props) {
        super(props);
        this.state = {
            list: ['青花瓷', '一路向北', '听妈妈的话', '以父之名', '稻香', '夜曲'],
        }
    }

    render() {
        const {list} = this.state;
        return (
            <>
                <button onClick={this.addSong}>添加</button>
                <TransitionGroup>
                    {
                        list && list.map((item, index)=>{
                            return(
                                /*
                                在TransitionGroup中CSSTransition必须存在key，且需要是唯一的，
                                不要用数组索引，这样会导致移除动画总是出现在最后一条
                                */
                                <CSSTransition
                                    key={item}
                                    classNames={'card'}
                                    timeout={600}
                                >
                                    <div style={{height: 36, lineHeight: '36px'}} onClick={()=>{
                                        this.deleteSong(index)
                                    }}>
                                        {item}
                                    </div>
                                </CSSTransition>
                            )
                        })
                    }
                </TransitionGroup>
            </>
        )
    }

    deleteSong = (index)=>{
        let {list} = this.state;
        list.splice(index, 1);
        this.setState({
            list
        })
    }

    addSong = ()=>{
        let {list} = this.state;
        list.push('晴天' + Date.now())
        this.setState({
            list
        })
    }

}
```

