## 1.Less简介

>   Less 是一门 CSS 预处理语言，它扩展了 CSS 语言，增加了变量、Mixin、函数等特性，使 CSS 更易维护和扩展。Less 可以运行在 Node 或浏览器端。Less最终会被编译成css。



## 2.使用webpack解析less

```js
//webpack.config.js

const {resolve} = require('path');
let HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: './src/index.js',
    output: {
        filename: "build.js",
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [
            {
                test: /\.css/,
                use: ['style-loader', 'css-loader']
            },
            {
                test: /\.less/,
                use: ['style-loader', 'css-loader', 'less-loader']
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        })
    ],
    mode: "development",
    devServer: {
        contentBase: resolve(__dirname, 'build'),
        compress: true,
        port: 3006,
        open: true
    }
};
```





## 3.基本规则



### 1.less中的注释

```js
less中注释只能使用 /**/
```



### 2.less中的变量

使用@来申明一个变量：@pink：pink;
1.作为普通属性值只来使用：直接使用@pink
2.作为选择器和属性名：#@{selector的值}的形式
3.作为URL：@{url}

```less
/*
使用@来申明一个变量：@pink：pink;
1.作为普通属性值可以直接使用 color: @pink;
2.作为选择器和属性名的用法： @{m}: 0
3.作为URL： @{url}
*/
@mainColor: skyblue;
@m: margin;
@select: #wrap;
@bgImage: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1584639711892&di=e5c14e6e67914462ec497cad06ad3a0a&imgtype=0&src=http%3A%2F%2Fa0.att.hudong.com%2F78%2F52%2F01200000123847134434529793168.jpg';

* {
  /*作为属性名*/
  @{m}: 0;
  padding: 0
}

/*作为选择器*/
@{select} {
  position: relative;
  width: 600px;
  height: 600px;
  /*作为属性值*/
  border: 1px solid @mainColor;
  margin: 100px auto;
  .inner{
    position: absolute;
    width: 150px;
    height: 150px;
    background: url("@{bgImage}");
    top: 50%;
    left: 50%;
    transform: translate3d(-50%, -50%, 0);
  }
}
```



4.变量的延迟加载

```less
/*定义一个变量num，值为0*/
/*变量存在块级作用域，.inner里面可以访问{}外的变量*/
/*需要注意的是，变量存在延迟加载的特性，所以.inner中的padding的值为2 而不是1 */
@num: 0;
.out{
  @num: 1;
  .inner{
    padding: @num;
    @num: 2
  }
}
```





### 3.less中的嵌套规则

在一个选择器中直接加入另一个选择器，编译成css后会以空格隔开

在一个选择器中加入另一个以&符号开头的选择器则会省略空格，直接拼接

```less
/*index.less*/
#wrap {
  position: relative;
  width: 600px;
  height: 600px;
  border: 1px solid skyblue;
  margin: 100px auto;
  .inner{
    position: absolute;
    width: 150px;
    height: 150px;
    background: skyblue;
    top: 50%;
    left: 50%;
    transform: translate3d(-50%, -50%, 0);
    //给.inner添加hover处理
    &:hover{
      background: deeppink;
    }
  }
}
```



转换为index.css

```css
/*index.css*/
#wrap {
  position: relative;
  width: 600px;
  height: 600px;
  border: 1px solid skyblue;
  margin: 100px auto;
}
#wrap .inner {
  position: absolute;
  width: 150px;
  height: 150px;
  background: skyblue;
  top: 50%;
  left: 50%;
  transform: translate3d(-50%, -50%, 0);
}
#wrap .inner:hover {
  background: deeppink;
}

```













