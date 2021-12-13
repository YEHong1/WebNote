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

如果不想使用webpack，可以使用cdn

```html
<link rel="stylesheet/less" type="text/css" href="index.less" />
<!-- 必须加上/less -->
<script src="less-1.3.3.min.js"></script>
<!-- js必须在less后引用 -->

通过以上配置，在访问页面时，js会自动编译less为css
```



## 3.基本规则

### 变量

使用@来申明一个变量：@pink：pink;
1.作为普通属性值来使用：直接使用@pink
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



### 变量的延迟加载

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



### 嵌套规则

在一个选择器中直接加入另一个选择器，编译成css后会以空格隔开

在一个选择器中加入另一个以&符号开头的选择器则会省略空格，直接拼接

```less
/*index.less*/
@color: pink;
@textColor: #fff;

#root{
	width: 300px;
	height: 300px;
	background-color: @color;
	/* #root > p*/
	&>p{
		color: @textColor;
	}
	/* #root:hover*/
	&:hover{
		background-color: @textColor;
	}
}

```



### 混合 （ `mixin`）

混合可以将一个定义好的class A轻松的引入到另一个class B中，从而简单实现class B继承class A中的所有属性。我们还可以带参数地调用，就像使用函数一样。 

```less
/*@fontSize和@color都是形参，冒号后面的值是形参默认值*/
.classA(@fontSize: 18, @color: #fff){
	font-size: @fontSize;
	color: @color;
}

.classB{
    font-style: normal;
    /*不传参使用混合，表示使用默认值*/
    .classA
}

.classC{
    font-style: italic;
    /*传递参数使用混合*/
    .classA(16, pink)
}
```



### 运算

```less
/*任何数字、颜色或者变量都可以参与运算. 来看一组例子:*/

@base: 5%;
@filler: @base * 2;
@other: @base + @filler;

color: #888 / 4;
background-color: @base-color + #111;
height: 100% / 2 + @filler;
/*LESS 的运算已经超出了我们的期望，它能够分辨出颜色和单位。如果像下面这样单位运算的话:*/

@var: 1px + 5;
/*LESS 会输出 6px.*/

/*括号也同样允许使用:*/
width: (@var + 5) * 2;

/*并且可以在复合属性中进行运算:*/
border: (@width * 2) solid black;
```



### Math函数

```less
/*LESS提供了一组方便的数学函数，你可以使用它们处理一些数字类型的值:*/

round(1.67); // returns `2`
ceil(2.4);   // returns `3`
floor(2.6);  // returns `2`
/*如果你想将一个值转化为百分比，你可以使用percentage 函数:*/

percentage(0.5); // returns `50%`
```



### Import

```less
/*你可以在main文件中通过下面的形势引入 .less 文件, .less 后缀可带可不带*/
@import "lib.less";
@import "lib";
/*如果你想导入一个CSS文件而且不想LESS对它进行处理，只需要使用.css后缀就可以*/
@import "lib.css";
```



### 字符串插值

```less
/*变量可以用类似ruby和php的方式嵌入到字符串中，像@{name}这样的结构*/
@base-url: "http://assets.fnord.com";
background-image: url("@{base-url}/images/bg.png");
```



### JavaScript 表达式

```less
// JavaScript 表达式也可以在.less 文件中使用. 可以通过反引号的方式使用:

@var: `"hello".toUpperCase() + '!'`;
// 输出:
// @var: "HELLO!";

// 它也可以访问JavaScript环境:
@height: `document.body.clientHeight`;
```

