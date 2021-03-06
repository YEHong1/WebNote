

## 1.1. hook是什么

 *Hook* 是 React 16.8 的新增特性，它可以让我们在不编写class的情况下使用state以及其他的React特性（比如生命周期）。 



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
-    在依赖不变的情况下，多次定义的时候，返回的值是相同的 

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
* 而increment2每一渲染函数时都需要重新创建，返回值是一个新的内存地址，每一次都不一样
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





