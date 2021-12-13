## 1.TypeScript介绍

1. TypeScript是JavaScript的超集。
2. 它对JS进行了扩展，向JS中引入了类型的概念，并添加了许多新的特性。
3. TS代码需要通过编译器编译为JS，然后再交由JS解析器执行。
4. TS完全兼容JS，换言之，任何的JS代码都可以直接当成JS使用。
5. 相较于JS而言，TS拥有了静态类型，更加严格的语法，更强大的功能；TS可以在代码执行前就完成代码的检查，减小了运行时异常的出现的几率；TS代码可以编译为任意版本的JS代码，可有效解决不同JS运行环境的兼容问题；同样的功能，TS的代码量要大于JS，但由于TS的代码结构更加清晰，变量类型更加明确，在后期代码的维护中TS却远远胜于JS。



## 1.1 TypeScript 和 JavaScript的区别

| TypeScript                                                   | JavaScript                               |
| ------------------------------------------------------------ | ---------------------------------------- |
| JavaScript 的超集用于解决大型项目的代码复杂性JavaScript 的超集用于解决大型项目的代码复杂性 | 一种脚本语言，用于创建动态网页           |
| 可以在编译期间发现并纠正错误                                 | 作为一种解释型语言，只能在运行时发现错误 |
| 强类型，支持静态和动态类型                                   | 弱类型，没有静态类型选项                 |
| 最终被编译成 JavaScript 代码，使浏览器可以理解               | 可以直接在浏览器中使用                   |
| 支持模块、泛型和接口                                         | 不支持模块，泛型或接口                   |
| 支持 ES3，ES4，ES5 和 ES6 等                                 | 不支持编译其他 ES3，ES4，ES5 或 ES6 功能 |
|                                                              |                                          |



## 2.TypeScript环境搭建

命令行的 TypeScript 编译器可以使用 Node.js 包来安装。

**安装 TypeScript**

```shell
npm i -g typescript
```

**编译 TypeScript 文件**

```shell
tsc helloTs.ts
编译后会生成一个对应的js文件
```



## 3.基本类型

### 类型声明

-   类型声明是TS非常重要的一个特点

-   通过类型声明可以指定TS中变量（参数、形参）的类型

-   指定类型后，当为变量赋值时，TS编译器会自动检查值是否符合类型声明，符合则赋值，否则报错

-   简而言之，类型声明给变量设置了类型，使得变量只能存储某种类型的值

-   TS拥有自动的类型判断机制，当对变量的声明和赋值是同时进行的，TS编译器会自动判断变量的类型，所以如果你的变量的声明和赋值时同时进行的，可以省略掉类型声明

    ```typescript
    // let 变量: 类型;
    let str: string;
    
    // let 变量: 类型 = 值;
    let age: number = 18;
    
    // function fn(参数: 类型, 参数: 类型): 类型{} 括号对应的类型代表函数返回值的类型
    function add(a: number, b: number): string {
      return a + b + ''
    }
    
    // ts会根据定义变量时的值自动判断变量类型
    let flag = true;
    flag = 1; // 提是错误，flag为布尔值类型
    
    // 补充一个概念，我们手动去声明一个变量的类型，这种行为叫做类型注解
    // ts感觉变量的赋值表达式推断变量的类型，这种行为叫做类型
    ```



### TS的基本类型

|  类型   |       例子        |              描述              |
| :-----: | :---------------: | :----------------------------: |
| number  |    1, -33, 2.5    |            任意数字            |
| string  | 'hi', "hi", `hi`  |           任意字符串           |
| boolean |    true、false    |       布尔值true或false        |
| 字面量  |      其本身       |  限制变量的值就是该字面量的值  |
|   any   |         *         |            任意类型            |
| unknown |         *         |         类型安全的any          |
|  void   | 空值（undefined） |     没有值（或undefined）      |
|  never  |      没有值       |          不能是任何值          |
| object  |  {name:'孙悟空'}  |          任意的JS对象          |
|  array  |      [1,2,3]      |           任意JS数组           |
|  tuple  |       [4,5]       | 元组，TS新增类型，固定长度数组 |
|  enum   |    enum{A, B}     |       枚举，TS中新增类型       |



-   `number`

    ```typescript
    let decimal: number = 6;
    ```

-   `string`

    ```typescript
    let color: string = "blue";
    ```

-   `boolean`

    ```typescript
    let isDone: boolean = false;
    ```

-   字面量

    ts也可以使用字面量去指定变量的类型，通过字面量可以确定变量的取值范围

    ```typescript
    let color: 'red' | 'blue' | 'black';
    let num: 1 | 2 | 3 | 4 | 5;
    
    // color 的值 只能是 'red' 、 'blue' 、 'black' 其中一个
    ```

-   `any`

    `any`表示任意类型个，一个变量设置类型为 `any` 相当于对改变了关闭了 `ts` 的类型检测 （不建议使用）
    
    ```js
    let a: any;
    a = 10;
    a = 'hello world';
    a = true;
    
    // 如果声明了一个变量，没有指定类型，且没有赋值，那么这变量的类型为 any，这种称为隐式 any
    let b;
    b = 10;
    b = 'hello';
    ```
    
-   `unknown`

    `unknown`表示一个未知类型的值

    ```js
    // unknown 看起来和 any 一样，可以赋予任意类型的值
    let a: unknown;
    a = 6;
    a = 'hello world';
    a = true;
    
    // unknown 和 any 有什么区别呢？
    let b: any;
    b = 10;
    
    let c: string;
    // any 可以赋值给任意类型的变量
    c = b;
    console.log(c); // 数字 10
    
    // unknown 实际上是一个类型安全的 any
    // unknown 类型的变量不能直接赋值给其他变量
    c = a; // 报错
    ```

-   `void`

    表示 空值 或者 undefined，一般用在指定函数返回值上，表示这个函数没有返回值或返回值为 `undefined`

    ```js
    function fn(): void{
        // 下面三种都可以
        // return;
        // return null;
        // return undefined;
    }
    
    ```

-   `never`

    表示没有值

    ```js
    function fn(): never{
      // 没有返回值，返回undefined都不可以
      // js中抛出错误的时候，程序不再往下执行，这时候就不会有任何返回值
      throw new Error('hello');
    }
    
    ```

-   `object`

    ```js
    // object 表示一个js对象
    let a: object;
    a = {};
    a = function (){
    
    }
    
    // 我们如果希望定义一个指定格式的对象，可以这样写
    // { 属性名: 属性值类型 }
    // 在属性名后面加上 ？ 表示属性是可选的
    let b: { name: string, age?: number };
    b = { name: '张三' };
    b = { name: '阿卡丽', age: 18 };
    b = { name: '阿卡丽', height: 180 }; // 报错，指定的格式里没有height属性
    
    // 如果我们希望对象可以包含指定格式之外的其他属性时，可以这样写
    // [ propName: string ]: any 表示任意类型的属性 propName 可以是其他单词
    let c: { name: string, [keyName: string]: any };
    c = { name: '亚索', height: 180 }
    
    ```

-   `array`

    ```js
    // 数组的定义方式有两种， 用于表示某种类型的数组
    // 1. let 变量: 类型[]
    // 2. let 变量: Array<类型>
    let a: string[];
    a = ['hello', 'hi'];
    let b: Array<number>;
    b = [6, 26];
    ```

-   `tuple`

    ```js
    // 表示固定长度的数组
    let a: [string, number];
    a = ['hello', 6];
    ```

-   `enum`

    ```js
    enum Color {
      Red= 1,
      Green = 2,
      Blue = 3,
    }
    let a: Color = Color.Blue;
    console.log(a); // 3
    ```

    

### 函数声明

```ts
// 接受两个数字类型的参数，返回一个数字类型的返回值
let fn: (a: number, b: number)=> number;
fn = function (a, b){
  return a + b;
}
```



### 类型注解

```ts

```

