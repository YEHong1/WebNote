## 

## 1.创建基于TypeScript的react项目

```js
npx create-react-app 项目名称 --template typescript
// 值得注意的是：react、 react-dom 这些包并不支持 TypeScript
// 所以我们需要下载 @types/... 这些包, 这些包只参与开发阶段的编译，实际打包并不需要，所以可以放在开发中，以减少最终的打包体积

// npm i @/types/react @types/react-dom @types/react-router-dom -D
```



## 2.组件中使用TypeScript

### 函数组件

```tsx
import React, {useState} from 'react';
import style from './App.module.css';

// 定义接受的props的参数类型
interface PropsType{
  name: string,
  age: number
}

function App({ name, age }: PropsType) {
  // useState<T> 泛形 T表示类型
  const [count, setCount] = useState<number>(0);

  return (
    <div className={style.container}>
      Hello world {name}, {age}
      <span onClick={()=> setCount(count + 1)}>{count}</span>
    </div>
  );
}

export default App;

// 注意： 我们经常看到一种写法  const App: React.FC = ()=>{}  这里的FC表示函数式组件
```



### 类组件

```tsx
import React, { PureComponent } from 'react';

// 定义接受的props的参数类型
interface PropsType{
  name: string,
  age: number
}

// 定义state的类型
interface StateType{
  count: number
}

// 类组件也可以通过泛形来给 props 和 state 添加类型约束
class App extends PureComponent<PropsType, StateType>{

  constructor(props: PropsType) {
    super(props);
    this.state = {
      count: 0
    }
  }

  render(){
    return(
      <div>
        Hello World
      </div>
    )
  }
}

export default App;

```



## 3.路由组件中使用TypeScript

```js

```

