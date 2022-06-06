

## 1.1. hook是什么

 `hook` 是 React 16.8 的新增特性，它可以让我们在不编写class的情况下使用state以及其他的React特性（比如生命周期）。 

使用`Hooks`的原则：

- 只在`react`函数中使用`hook`，不能在`JavaScript`函数中使用
- 只在最顶层使用`hook`，不要在循环，条件或嵌套函数中调用 `hook`



## 2.基本使用

## 2.1 useState

useState用于在函数之间中定义和使用state

```jsx
// useState来自react，需要从react中导入，它是一个hook 
// userState是函数类型，接受一个参数，作为默认值
// 返回一个数组，数组的第一个元素是 变量值， 第二个元素是函数，用来设置变量的值
// const [变量值, 设置变量的方法] = useState(变量默认值)

import React, {useState} from "react";

export default function App() {
    const [count, setCount] = useState(0);
    const [userInfo, setUserInfo] = useState({
        name: '张三',
        age: 26,
        phone: '18312345678',
        address: '广东省广州市天河区珠江新城'
    })

    return(
        <div>
            <span onClick={changeCount}>App</span>
            <p>{count}</p>
            <p onClick={changeUserInfo}>用户信息</p>
            <p>姓名: {userInfo.name}</p>
            <p>姓名: {userInfo.age}</p>
            <p>姓名: {userInfo.phone}</p>
            <p>姓名: {userInfo.address}</p>
        </div>
    )

    function changeCount(){
        // setCount（变量值） 和 类组件中的setState一样 多个同时使用时会被合并
        // 所以这里使用函数作为参数来解决合并问题
        setCount(preCount => preCount + 1)
        setCount(preCount => preCount + 1)
        setCount(preCount => preCount + 1)
        setCount(preCount => preCount + 1)
        setCount(preCount => preCount + 1)
        setCount(preCount => preCount + 1)
    }

    function changeUserInfo(){
        // 改变引用数据类型时需要改变内存地址才有效
        const info = {...userInfo};
        info.name = '无际';
        setUserInfo(info)
    }
}
```



## 2.2 useEffect

`useEffect` 被称为副作用钩子函数，那我们就来说一下什么是副作用

-   首先解释`纯函数（Pure function）`：给一个 function 相同的参数，永远会返回相同的值，并且没有副作用；这个概念拿到 React 中，就是给一个 Pure component 相同的 props, 永远渲染出相同的视图，并且没有其他的副作用；纯组件的好处是，容易监测数据变化、容易测试、提高渲染性能等；
-   副作用（Side Effect）是指一个 function 做了和本身运算返回值无关的事，比如：修改了全局变量、修改了传入的参数、甚至是 console.log()



useEffect用来代替生命周期, 存在多个useEffect时，按定义顺序执行

```jsx
// App.js
import React, {useState} from "react";
import TestUseEffect from "./components/TestUseEffct";

export default function App() {
    const [isLogin, setIsLogin] = useState(false);
    const [isShow, setIsShow] = useState(true);

    return(
        <>
            <h2>APP</h2>
            <p onClick={()=>{
                setIsLogin(!isLogin)
            }}>{isLogin ? '阿卡丽' : '未登录'}</p>
            <hr/>
            <p onClick={()=>{
                setIsShow(!isShow)
            }}>{isShow ? '隐藏TestUseEffect' : '显示TestUseEffect'}</p>
            {isShow && <TestUseEffect/>}
        </>
    )

}
```

```jsx
// TestUseEffect.js
import React, {useState, useEffect} from "react";

export default function TestUseEffect () {
	const [count, setCount] = useState(0);

	/*
	* 没有第二个参数，无论该组件是第一次渲染还是更新都会执行回调函数中的内容
	* 类似类组件的componentDidUpdate
	* */
	useEffect(()=>{
		console.log('TestUseEffect update')
	})

	/*
	* 存在参数二时，只有参数2中依赖的state初始化和发生改变时才会执行回调函数
	* 注意： 参数2必须为数组，数组中的值对应 组件中定义的state
	* */
	useEffect(()=>{
		console.log('count发生了改变, 当前count为' + count);
	}, [count])

	/*
	* 参数1的回调函数也可以返回一个回调函数，这个返回的回调函数会在组件被销毁时调用
	* 如果想要实现类似于类组件componentWillUnmount的效果，需要加上参数2，且参数2为一个空数组
	* */
	useEffect(()=>{
		return ()=>{
			console.log('TestUseEffect被销毁!')
		}
	}, [])

	/*
	* 想要实现 类组件componentDidMount的效果
	* 参数2传递一个空数组，且参数1没有返回回调函数即可
	* */
	useEffect(()=>{
		setTimeout(()=>{
			console.log('请求获取数据完成')
		}, 1000)
	}, [])

	return(
		<div style={{height: 150, backgroundColor: 'skyblue', overflow: 'hidden'}}>
			<h2>TestUseEffect</h2>
			<p onClick={()=>{
				setCount(count + 1)
			}}>{count}</p>
		</div>
	)

}
```



### 注意点：不要在 `useEffect`中使用 `async await`

```js
// 不要用下面的写法
useEffect(async()=>{
    await axios('GET', url);
}, [])

// 建议用一个函数包起来，再在useEffect中调用
const getData = async()=>{
    await axios('GET', url);
}

useEffect(()=>{
    getData();
}, [])

// 为什么要这样呢？这是因为async 函数会返回一个promise对象，而useEffect只能接受函数或者null作为返回值。返回promise可能会出现预期之外的结果
```



## 2.3 useContext

多个Context共享时的方式会存在大量的嵌套 。

Context Hook允许我们通过Hook来直接获取某个Context的值 

```jsx
// App.js
import React from 'react';
import Child from "./components/Child";

export const UserContext = React.createContext({name: '', age: 0, phone: ''});
export const ThemeContext = React.createContext({fontSize: '15px', backgroundColor: 'skyblue'});
export default function App(){
    return(
        <div>
            <h2>App</h2>
            <UserContext.Provider value={{name: '张三', age: 18, phone: '18312345678'}}>
                <ThemeContext.Provider value={{fontSize: '15px', backgroundColor: 'pink'}}>
                    <Child/>
                </ThemeContext.Provider>
            </UserContext.Provider>
        </div>
    )
}
```

```jsx
// Child.js
import React, {useContext} from "react";
import {UserContext, ThemeContext} from '../App';

export default function Child(){
	// 获取context的值
	const user = useContext(UserContext);
	const theme = useContext(ThemeContext);

	console.log(user, theme);
	return(
		<div>
			<h2>Child</h2>
		</div>
	)
}
```



## 2.4 memo

当父组件的`state`或者`prop`发生变化时，会触发子组件的重新渲染，即使这个子组件对父组件的state没有任何依赖。我们想要像类组件的 pureComponent 那样，当子组件的props没有发生变化时，父组件的的更新不会导致子组件的重新渲染。这个时候只需要将这个子组件作为参数传递给memo函数即可。

`memo`和`PureComponent`作用类似，可以用作性能优化，`memo` 是高阶组件，函数组件和类组件都可以使用， 和`PureComponent`的区别是 `memo`只能针对`props`的情况确定是否渲染，而`PureComponent`是针对`props`和`state`。

`memo` 接受两个参数，第一个参数原始组件本身，第二个参数，可以根据一次更新中`props`是否相同决定原始组件是否重新渲染。是一个返回布尔值，`true` 证明组件无须重新渲染，`false`证明组件需要重新渲染，这个和类组件中的`shouldComponentUpdate()`正好相反 。

`memo`： 第二个参数 返回 true 组件不渲染 ， 返回 false 组件重新渲染。
`shouldComponentUpdate`： 返回 true 组件渲染 ， 返回 false 组件不渲染。

```jsx
// 基础例子
import React, {memo, useState} from "react";

const Game = ()=>{

  const [num, setNum] = useState(0);

  console.log('game is render')

  return(
    <div>
      <p onClick={()=> setNum(num + 1)}>{num}</p>
      Game
    </div>
  )
}

export default memo(Game);

```



```jsx
// 需求，组件有一个props：count，当count发生改变且改变后的值小于6时，组件才会重新渲染
import { memo, useEffect } from 'react';

const Child: React.FC<propsType> = ({ count })=>{

  useEffect(()=>{
    console.log(count);
  }, [count])

  return(
    <div>
      <p>Child</p>
      <span>count: {count}</span>
    </div>
  )
}

export default memo(Child, (pre, next)=>{
  // 返回false时表示需要重新渲染
  return (next.count === pre.count || next.count > 6)
})

```





## 2.5 自定义hook

当我们有多个组件有共同的逻辑的时候就可以考虑使用自定义`hook`

自定义`hook`是一个函数，函数名称以`use`开头，函数内部可以调用其它的`hook`

自定义`hook`和函数组件的区别在于：**自定义 `Hook` 函数偏向于功能，而组件偏向于界面和业务逻辑**

下面是一个例子，用来监听浏览器尺寸的的变化

```react
// 自定义hook
import { useState, useEffect, useCallback } from 'react';

const useWinSize = ()=>{
	const [ size , setSize] = useState({
		width: document.documentElement.clientWidth,
		height: document.documentElement.clientHeight
	})

	const onResize = useCallback(()=>{
		setSize({
			width: document.documentElement.clientWidth,
			height: document.documentElement.clientHeight
		})
	},[])

	useEffect(()=>{
		window.addEventListener('resize', onResize)
		return ()=>{
			window.removeEventListener('resize', onResize)
		}
	},[])

	return size;
}

export default useWinSize();


// 使用方法： 只需要在react组件中引入并调用即可
const size = useWinSize();
```



## 3.高级使用

## 3.1 useReducer

 useReducer是useState的一种替代方案，与redux没有关系

-    在某些场景下，如果state的处理逻辑比较复杂，我们可以通过useReducer来对其进行拆分 
-    或者这次修改的state需要依赖之前的state时，也可以使用 

```jsx
// App.js
import React, {useReducer} from 'react';

function reducer(state, action){
    switch (action.type){
        case 'increment':
            return {...state, count: state.count + 1}
        case 'decrement':
            return {...state, count: state.count - 1}
        default:
            return state
    }
}

export default function App(){
    /*
    * useReducer 一般接受2个参数(可以接受3个)
    * 第一个参数是一个纯函数(和redux的reducer函数一样)
    * 第二个参数代表默认值
    * */
    const [state, dispatch] = useReducer(reducer, {
        count: 0
    })

    return(
        <div>
            <h2>App</h2>
            <p>{state.count}</p>
            <p onClick={()=>{dispatch({type: 'increment'})}}>Add</p>
            <p onClick={()=>{dispatch({type: 'decrement'})}}>Sub</p>
        </div>
    )
}
```



## 3.2 useCallback（对函数进行优化）

 useCallback实际的目的是为了进行性能的优化。 

-    useCallback会返回一个函数的 memoized（记忆的）值 
-    在依赖不变的情况下，多次定义的时候，返回的值是相同的 （内存地址相同）

```jsx
const increment1 = useCallback(()=>{
    console.log('increment1');
}, [])
```

 我们来看下面一段很有趣的代码 

-    increment1在每次函数组件重新渲染时，会返回相同的值 
-    increment2每次定义的都是不同的值 

问题： 是否increment1会比increment2更加节省性能呢？ 

-    事实上，经过一些测试，并没有更加节省内存，因为useCallback中还是会传入一个函数作为参数 
-    所以并不存在increment2每次创建新的函数，而increment1不需要创建新的函数这种性能优化 

 那么，为什么说useCallback是为了进行性能优化呢？ 

```jsx
import React, { useState, useCallback } from 'react'

export default function App() {
    const [count, setCount] = useState(0);

    const increment1 = useCallback(function increment() {
        setCount(count + 1);
    }, []);

    const increment2 = function() {
        setCount(count + 1);
    }

    return (
        <div>
            <h2>当前计数: {count}</h2>
            <button onClick={increment1}>+1</button>
            <button onClick={increment2}>+1</button>
        </div>
    )
}
```

 我们来对上面的代码进行改进 ：

-    在下面的代码中，我们将回调函数传递给了子组件，在子组件中会进行调用 
-    在发生点击时，我们会发现接受increment1的子组件不会重新渲染，但是接受increment2的子组件会重新渲染 
-    所以useCallback最主要用于性能渲染的地方应该是和memo结合起来，决定子组件是否需要重新渲染 

```jsx
import React, { useState, useCallback, memo } from 'react'

const ButtonComponent = memo((props)=>{
    const {increment, title} = props;
    console.log(`render ${title}`);
    return(
        <button onClick={increment}> +1 </button>
    )
})

/*
* 这个例子中， increment1使用了useCallback，在依赖项count没有发生改变时，返回值都是一样的
* 而increment2每一次渲染函数时都需要重新创建，返回值是一个新的内存地址，每一次都不一样
* 当用户修改count时，increment1因为依赖项count发生改变，返回值也发生改变，
* 所以count改变时，ButtonComponent1和ButtonComponent2都会重新渲染
* 当用户改变isLogin时，increment1的返回值和上一次一样，所以此时只有ButtonComponent2重新渲染
* */
export default function App() {
    const [count, setCount] = useState(0);
    const [isLogin, setIsLogin] = useState(false);

    const increment1 = useCallback(()=> {
        setCount(count + 1);
    }, [count]);

    const increment2 = ()=> {
        setCount(count + 1);
    };

    return (
        <div>
            <h2>当前计数: {count}</h2>
            <ButtonComponent increment={increment1} title={'ButtonComponent1'}/>
            <ButtonComponent increment={increment2} title={'ButtonComponent2'}/>
            <p onClick={()=>{
                setIsLogin(!isLogin);
            }}>{isLogin ? 'hello' : '未登录'}</p>
        </div>
    )
}
```



## 3.3 useMemo（对返回值做优化）

 useMemo实际的目的也是为了进行性能的优化。 

-    useMemo返回的也是一个 memoized（记忆的） 值 
-    在依赖不变的情况下，多次定义的时候，返回的值是相同的 

下面是模拟计算属性的用法，也可以通过这种用法来减少子组件的渲染

```jsx
import React, {useState, useMemo} from "react";

/*
* useMemo传入2个参数
* 参数1 传入一个回调函数，并return一个值作为useMemo的返回值
* 参数2 依赖项数组，当依赖项发生改变时才执行参数1对应的回调函数
* 应用场景：类似于vue的计算属性，减少复杂操作的不必要执行
* */
export default function App(){
    const [count, setCount] = useState(10);
    const [isLogin, setIsLogin] = useState(false)

    /*计算0到count的和，count发生改变时重新计算*/

    // 这种做法会在函数重新渲染时重复执行，例如改变isLogin时，会重新计算total，这是没有必要的
    // let total = 0;
    // for(let i=0; i<count; i++){
    //     total += count;
    // }

    // 使用useMemo
    const total = useMemo(()=>{
        console.log('计算total的值');
        let total = 0;
        for(let i=0; i<count; i++){
            total += count
        }
        return total;
    }, [count])

    return(
        <div>
            <h2>App</h2>
            <p>total: {total}</p>
            <button onClick={()=>{ setCount(count + 1) }}> count + 1</button>
            <br/>
            {/*isLogin不是total的依赖项, 修改isLogin后，不会触发total的重新计算*/}
            <p onClick={()=>{ setIsLogin(!isLogin) }}> {isLogin ? 'Hello' : '登录'} </p>
        </div>
    )
}
```



```js
// useMemo 还可以用来代替 useCallback 返回一个记忆函数, 下面两个用法的功能都是等价的，区别在于，useCallback不会执行第一个参数，

// useCallback(fn, 依赖项) 
// useMemo(()=> fn, 依赖项) 
```





## 3.4 useRef

 useRef返回一个ref对象，返回的ref对象在组件的整个生命周期保持不变。 

 最常用的ref是两种用法： 

-    用法一：引入DOM元素或组件（这个组件需为class组件，用ref获取函数组件会报错） 
-    用法二：保存一个数据，这个对象在整个生命周期中可以保存不变 

用法1：

```jsx
import React, {useRef} from "react";

export default function App(){
    const inputRef = useRef();

    return(
        <div>
            <input ref={inputRef}/>
            <button onClick={()=>{
                const {value} = inputRef.current;
                console.log(value)
            }}>获取输入框的值</button>
        </div>
    )
}
```

用法2：

```jsx
import React, {useRef, useState, useEffect} from "react";

export default function App(){
    // numRef.current在组件的整个生命周期中的值都为10
    // const numRef = useRef(10);

    // 如果我们有一个count变量，且希望记录它改变前的值
    const [count, setCount] = useState(0);
    const numRef = useRef(0);

    useEffect(()=>{
        // 修改count后执行
        numRef.current = count
    }, [count])

    return(
        <div>
            <p>上一次count的值: {numRef.current}</p>
            <p>当前count的值: {count}</p>
            <button onClick={()=>{
                setCount(count + 1)
            }}> count + 1 </button>
        </div>
    )
}
```



上面说到ref获取组件需要该组件为class组件，否则会出错。其实也有方法可以获取到函数组件

```jsx
// 对函数式组件使用ref，会有以下错误提示：
// Warning: Function components cannot be given refs. Attempts to access this ref will fail. 
// Did you mean to use React.forwardRef()

// 提示我们使用 React.forwardRef()。 以下为正确用法

// 通过forwardRef可以将ref转发到子组件，子组件拿到父组件中创建的ref，绑定到自己的某一个元素中
import React, {useRef, forwardRef} from "react";

const AppChild = forwardRef((props, ref)=>{
    return <input ref={ref}/>
})

export default function App(){
    const inputRef = useRef();
    return(
        <div>
            <AppChild ref={inputRef}/>
            <button onClick={()=>{
                console.log(inputRef.current.value)
            }}>获取AppChild中Input的value值</button>
        </div>
    )
}
```



## 3.5 useImperativeHandle

在上面的 forwardRef 使用示例中， 我们是将子组件的DOM直接暴露给了父组件。

-    直接暴露给父组件带来的问题是某些情况的不可控 
-    父组件可以拿到DOM后进行任意的操作 

如果我们希望父组件只能进行某些我们规定的操作，那么可以使用 useImperativeHandle

```jsx
import React, {useRef, forwardRef, useImperativeHandle} from "react";

/*
* useImperativeHandle 可以传递3个参数
* 参数1 传递进来的ref
* 参数2 传递一个函数，这个函数返回一个对象。但父组件使用ref.current时获取到的值就算这个返回的对象
* 参数3 依赖项数组
* */
const AppChild = forwardRef((props, ref)=>{
    const inputRef = useRef();
    useImperativeHandle(ref, ()=> ({
        focus(){
            inputRef.current.focus()
        },
        getValue(){
            return inputRef.current.value;
        }
    }))

    return <input ref={inputRef}/>
})

export default function App(){
    const inputRef = useRef();
    return(
        <div>
            <AppChild ref={inputRef}/>
            <br/>
            <button onClick={()=>{
                inputRef.current.focus();
            }}>focus</button>
            <br/>
            <button onClick={()=>{
                console.log(inputRef.current.getValue())
            }}>getValue</button>
        </div>
    )
}
```



## 3.6  useLayoutEffect

useLayoutEffect看起来和useEffect非常的相似，事实上他们也只有一点区别而已：

-    useEffect会在渲染的内容更新到DOM上后执行，不会阻塞DOM的更新 
-    useLayoutEffect会在渲染的内容更新到DOM上之前执行，会阻塞DOM的更新 

类似于类组件中的componentWillUpdate

```jsx
import React, {useLayoutEffect, useState} from "react";

export default function App() {
    const [count, setCount] = useState(10);

    useLayoutEffect(()=>{
        alert('在count改变前执行逻辑')
    }, [count])

    return(
        <div>
            <p>count的值为：{count}</p>
            <button onClick={()=>{
                setCount(count + Number((Math.random() * 10).toFixed(0)))
            }}>change count</button>
        </div>
    )
}
```

```jsx
// useEffect能解决99%的场景，那么什么时候应该用 useLayoutEffect 呢？

import {useState, useLayoutEffect} from "react";

const App = () => {
  const [count, setCount] = useState(0);
  
  useLayoutEffect(() => {
    count === 0 && setCount(Date.now());
  }, [count]);

  return (
    <div onClick={() => setCount(0)}>
      {count}
    </div>
  );
};

export default App;

// 该案例中，如果使用 useEffect 来监听 count的变化，当用户快速多次点击div时，count的值会页面中疯狂闪烁 ( 在 0 和 时间戳中频繁切换 )
// 利用 useLayoutEffect 可以阻塞DOM渲染的特性，DOM中的count永远不会为0，也就不会出现闪烁的情况

相比使用 useEffect，当你点击 div，count 更新为 0，此时页面并不会渲染，而是等待 useLayoutEffect 内部状态修改后，才会去更新页面，所以页面不会闪烁。

总结：useLayoutEffect 相比 useEffect，通过同步执行状态更新可解决一些特殊场景下的页面闪烁问题。
```



