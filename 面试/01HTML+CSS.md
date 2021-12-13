

## 1.什么是标签语义化

让合适的标签去做自己合适的事情，让网页在不需要样式的情况下就能大致理解网页的结构。 



## 2.标签语义化的意义

-    代码结构清晰，方便阅读 

-    有利于搜索引擎优化（SEO），有助于爬虫抓取更多的有效信息 

-    有利于开发团队的后期优化开发以及维护 

​    

## 3.常见的语义化标签有哪些

```html
<title>：页面主体内容

<h->：h1~h6，分级标题

<ul>：无序列表

<ol>：有序列表

<header>：页眉通常包括网站标志、主导航、全站链接以及搜索框

<nav>：标记导航，仅对文档中重要的链接群使用。

<main>：页面主要内容，一个页面只能使用一次。如果是web应用，则包围其主要功能

<article>：定义外部的内容，其中的内容独立于文档的其余部分

<section>：定义文档中的节（section、区段）。比如章节、页眉、页脚或文档中的其他部分

<aside>：定义其所处内容之外的内容。如侧栏、文章的一组链接、广告、友情链接、相关产品列表

<footer>：页脚，只有当父级是body时，才是整个页面的页脚

<small>：呈现小号字体效果，指定细则，输入免责声明、注解、署名、版权

<strong>：和 em 标签一样，用于强调文本，但它强调的程度更强一些

<em>：将其中的文本表示为强调的内容，表现为斜体

<mark>：使用黄色突出显示部分文本

<figure>：规定独立的流内容（图像、图表、照片、代码等）（默认有40px左右margin）

<figcaption>：定义 figure 元素的标题，应该被置于 figure 元素的第一个或最后一个子元素的位置

<cite>：表示所包含的文本对某个参考文献的引用，比如书籍或者杂志的标题

<blockquoto>：定义块引用，块引用拥有它们自己的空间

<q>：短的引述（跨浏览器问题，尽量避免使用）

<time>：datetime属性遵循特定格式，如果忽略此属性，文本内容必须是合法的日期或者时间格式

<abbr>：简称或缩写

<dfn>：定义术语元素，与定义必须紧挨着，可以在描述列表dl元素中使用

<del>：移除的内容

<ins>：添加的内容

<code>：标记代码

<meter>：定义已知范围或分数值内的标量测量。（Internet Explorer 不支持 meter 标签）

<progress>：定义运行中的进度（进程）
```



## 4.块级标签和行内标签的区别

```js
1.块级标签
// 宽度为100%，高度由内容撑开，独占一行
// 可以设置 margin padding border
// 常见块级标签： div p ul ol li dt dd h1 ~ h6

2.行内标签
// 宽度由内容撑开，默认水平排列
// 可以设置水平方向的 margin 和 padding
// 常见行内标签： span em i

3.还有一种是行内块元素
// 宽度自适应，水平排列，不会独占一行
// 可以设置 margin padding border
```



## 5.display有哪些常用值

```js
1.block
// 此元素将显示为块级元素

2.inline
// 此元素会被显示为内联元素

3.inline-block
// 此元素会被显示为行内块元素

4.none
// 不显示此元素，此元素不会再占据位置

5.table
// 此元素会作为块级表格来显示（类似 <table>）

6.flex
// 弹性布局
```



## 6.盒子水平垂直居中

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Title</title>
	<style>
		*{ margin: 0; padding: 0; }
		html,body{ width: 100%; height: 100%; position: relative }
	</style>
</head>
<body>
	<div class="box"/>
</body>
</html>

```

```css
/* 
1.定位配合margin: auto 设置top left bottom right都为0，再设置margin为auto即可
  缺点：box的宽高需要确定，如果没有设置width和height则会使box的宽高和父元素一致
*/
.box{
    position: absolute;
    width: 100px;
    height: 100px;
    background-color: deepskyblue;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
}

/* 
2.定位 left 和 top 为50% 使box左上角距离父元素左边距为父元素宽度的一半， 上边距为父元素高度的一半
  再将box向左移动box宽度的一半，box向上移动box高度的一半
  这种方法也是需要准确知道box的宽高
*/
.box{
    position: absolute;
    width: 100px;
    height: 100px;
    background-color: deepskyblue;
    top: 50%;
    left: 50%;
    margin-left: -50px;
    margin-top: -50px;
}

/* 
3.做法和2的原理一致，只是用了css3的 transform: translate(-50%, -50%)属性，
  translate中的值对应自身宽高，我们就不需要知道box的实际宽高了
  缺点是 transform 存在兼容性问题
*/
.box{
    position: absolute;
    width: 100px;
    height: 100px;
    background-color: deepskyblue;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

/* 
4.父元素使用flex布局
  缺点是 flex布局存在兼容性问题
*/
body{
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%;
}
.box{
    width: 100px;
    height: 100px;
    background-color: deepskyblue;
}

/* 
5.td（单元格元素）的 align="center" 及 valign="middle" 属性设置单元格内容的水平和垂直居中
  我们可以通过 display: table-cell 将box模拟成一个表格单元格 td,
  注意： 
  设置父盒子display: table-cell时，父盒子的宽高不可以是100%，
  这种方法原本是用于设置文本的，如果用在div上，需要设置div为行内块元素
*/
body{
    display: table-cell;
    width: 600px;
    height: 600px;
    text-align: center;
    vertical-align: middle;
}
.box{
    display: inline-block;
    width: 100px;
    height: 100px;
    background-color: deepskyblue;
}

/* 
6.js通过设置 offsetLeft offsetTop之类的api也可以实现
*/
```



## 7.盒模型

盒模型的组成部分有： margin	border	padding	content

盒模型包含两种：标准盒模型 和 IE盒模型（也叫怪异盒模型）

标准盒模型的width 是 content 的宽度

IE盒模型width，对应  content + padding + border 部分

```css
/* 设置div为标准盒模型 */
.div1{
    box-sizing: content-box;
}

/* 设置div为IE盒模型 */
.div1{
    box-sizing: border-box;
}
```

### 获取盒模型宽度（offsetWidth）

offsetWidth = 内容宽度 + 内边距 + 边框

```js
// 比如一个div的 width为100px padding为10px border为1px margin为10px
// 那么在标准盒模型下： offsetWidth = width + padding * 2 + border * 2 = 122

// 在width为100px的情况下，又设置了box-sizing为 border-box
// 那么offsetWidth就是100，因为在IE盒模型中 width包含了内容宽度、内边距和边框
```



## 8.margin纵向重叠问题

-   相邻元素的 `margin-top 与 margin-bottom` 会发生重叠。则两个元素的间隔距离取最大值
-   空白内容的 `<p></p>`也会重叠

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Title</title>
	<style>
		p{
			font-size: 16px;
			line-height: 1;
			margin-top: 10px;
			margin-bottom: 15px;
		}
	</style>
</head>
<body>
	<p>Hello World</p>
	<p></p>
	<p></p>
	<p>Today is a good day</p>
	<!--问： 第一个p标签和最后一个p标签的间隔是多少？-->
    <!--
		答案是15px
		先不考虑空白p标签的问题，假如第一个p标签和最后一个p标签是相邻的，
		你是不是觉得它们之间的间隔是 margin-bottom + margin-top = 15 + 10 = 25?
		实质上 纵向上的margin会发生重叠，间隔值以大的值为准
	-->
</body>
</html>

```



## 9.margin负值问题

-   `margin-top`为小于0的值时，元素自身向上移动
-   `margin-left`为小于0的值时，元素自身向左移动
-   `margin-right`为小于0的值时，右侧元素左移，自身不受影响
-   `margin-bottom`为小于0的值时，下方元素上移，自身不受影响



## 10.BFC的理解与应用

### BFC是什么

-   ` Block formatting context `，块级格式化上下文
-   一块独立渲染区域，内部元素的渲染不会影响边界以外的元素

### 形成BFC的常见条件

-   float 不是 none
-   position 是 absolute 或者 fixed
-   overflow 不是 visible
-   display 是 flex、inline-block 等

### 常见应用

主要用于清除浮动

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Title</title>
	<style>
		.container{
			overflow: hidden;
			background-color: pink;
		}

		.box{
			width: 300px;
			height: 300px;
			background-color: skyblue;
			float: left;
		}
	</style>
</head>
<body>
	<div class="container">
		<!--
			我们给box设置左浮动会使box脱离文档流，container容器无法被box撑开，会出现高度塌陷问题
			我们可以触发bfc来解决这个问题： 使用 overflow： hidden 触发bfc
		-->
		<div class="box"></div>
		<p>Hello World</p>
	</div>
</body>
</html>

```



## 11. float布局相关问题

### 如何实现圣杯布局和双飞翼布局

圣杯布局和双飞翼布局解决的问题是一样的，就是两边固定宽，中间自适应的三栏布局，中间栏要在放在文档流前面以优先渲染。 

圣杯布局和双飞翼布局解决问题的方案在前一半是相同的，也就是三栏全部float浮动，但左右两栏加上负margin让其跟中间栏div并排，以形成三栏布局。

不同在于解决”中间栏div内容不被遮挡“问题的思路不一样： 

圣杯布局，为了中间div内容不被遮挡，给容器设置了padding-left和padding-right后，再给左右两个div设置相对定位，让它们根据自身位置向左、右偏移自身的宽度。

双飞翼布局，为了中间div内容不被遮挡，直接在中间div内部创建子div用于放置内容，在该子div里用margin-left和margin-right为左右两栏div留出位置。 



##### 圣杯布局案例

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Title</title>
	<style>
		body{min-width: 1000px}
		.container{
			padding-left: 200px;
			padding-right: 300px;
		}
		.container > div{
			float: left;
		}
		.center{
			width: 100%;
			height: 300px;
			background-color: skyblue;
		}
		.left{
			width: 200px;
			height: 300px;
			background-color: darkcyan;
			margin-left: -100%;
			/*元素相对于自身位置偏移*/
			position: relative;
			right: 200px;
		}
		.right{
			width: 300px;
			height: 300px;
			background-color: orange;
			margin-left: -300px;
			position: relative;
			left: 300px;
		}
	</style>
</head>
<body>
	<!--
		圣杯布局：三栏布局，中间一栏最先加载和渲染（内容最重要）
		实现：
			center宽度为100%, left和right的宽度随意。
			给left、right、center添加左浮动，
			这时由于center宽为100%，left和right会被挤到下一行。
			给left设置margin-left: -100%,
			给right设置margin-left: right宽度的负值
			这时发现center里的内容会被left和right遮挡。
			给container设置padding-left为left的宽度，padding-right为width的宽度
			（注意：不要给container设置宽度）
			再将left、right元素以自身为定位偏移即可
	-->
	<div class="container">
		<div class="center">
			HTTP（HyperText Transfer Protocol）是万维网（World Wide Web）的基础协议。
				自 Tim Berners-Lee 博士和他的团队在1989-1991年间创造出它以来，HTTP已经发生了太多的变化，在保持协议简单性的同时，不断扩展其灵活性。
				如今，HTTP已经从一个只在实验室之间交换文件的早期协议进化到了可以传输图片，高分辨率视频和3D效果的现代复杂互联网协议。
		</div>
		<div class="left"></div>
		<div class="right"></div>
	</div>
</body>
</html>

```



##### 双飞翼布局案例

```html
<!doctype html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style>
		body{min-width: 1000px}
		.container > div{
			float: left;
		}
		.center{
			width: 100%;
			height: 300px;
			background-color: skyblue;
		}
		.center > .content{
			margin-left: 200px;
			margin-right: 300px;
		}
		.left{
			width: 200px;
			height: 300px;
			background-color: darkcyan;
			margin-left: -100%;
		}
		.right{
			width: 300px;
			height: 300px;
			background-color: orange;
			margin-left: -300px;
		}
	</style>
</head>
<body>
	<div class="container">
		<div class="center">
			<div class="content">
				HTTP（HyperText Transfer Protocol）是万维网（World Wide Web）的基础协议。
				自 Tim Berners-Lee 博士和他的团队在1989-1991年间创造出它以来，HTTP已经发生了太多的变化，在保持协议简单性的同时，不断扩展其灵活性。
				如今，HTTP已经从一个只在实验室之间交换文件的早期协议进化到了可以传输图片，高分辨率视频和3D效果的现代复杂互联网协议。
			</div>
		</div>
		<div class="left"></div>
		<div class="right"></div>
	</div>
</body>
</html>

```



### 手写 `clearfix`

```css
/*这个类名添加到子元素设置了浮动的容器中即可，例如上文的container容器*/
.clearfix:after{
    content: '';
    display: block;
    clear: both;
}
```



## 12.flex布局常用属性

```js
// http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html
```



## 13. absolute 和 relative 分别依据什么来定位

-   `relative` 依据自身定位
-   `absolute` 依据最近的定位元素定位



## 14. line-height 如何继承

`line-height`是可继承的，继承值由最近的设置了 `line-height`的祖先元素决定

##### 1.line-height: 固定像素

```css
body{
    font-size: 16px;
    line-height: 20px;
}
p{
    font-size: 18px;
}
/* p元素的 line-height 值为 20px */
```

##### 2.line-height: 百分比

```css
body{
    font-size: 16px;
    line-height: 150%;
}
p{
    font-size: 18px;
}
/* p元素的 line-height 值为 16 * 1.5 px */
```

##### 3.line-height: 数值

```css
body{
    font-size: 16px;
    line-height: 1.5;
}
p{
    font-size: 18px;
}
/* p元素的 line-height 值为 1.5 */
```

#####  **4.line-height: em** 

```css
body{
    font-size: 16px;
    line-height: 1.5em;
}
p{
    font-size: 18px;
}
/* 
em是父元素的字体大小
p元素的 line-height 值为 16 * 1.5 px 
*/
```



## 15. 媒体查询语法

##### max-width

```css
/*宽度超过1200px，则不适用media中的样式*/
@media screen and (max-width: 1200px){
    p{
        font-size: 18px
    }
}
```

##### min-width

```css
@media screen and (min-width: 750px){}
```

##### 多条件混用

```css
@media screen and (min-width: 750px) and (max-width: 1800px){}
```

还有 not 和 only 关键字，这两个关键字需要放在设备名称前，例如 `@media not screen and (max-width: 750px){}`



## 16.为何把css放在head中？

在渲染DOM节点前把CSS规则加载完，可以防止重复渲染。



## 17.为何建议把 js 放在body最后？

Render Tree 渲染页面时，如果遇到 `<script>` 则暂停渲染， 优先加载并执行JS代码， 完成再继续.
如果不把 `<script>` 放在最下面，会阻塞dom渲染 



## 18.window.onload和DOMContentLoaded的区别

```js
window.addEventListener('load',function(){
    // 页面加载完全部资源才执行，包括图片，视频
})

window.addEventListener('DOMContentLoaded',function(){
   // DOM渲染完即可执行，包图片，视频可能没加载完
})
```



## 19. 回流 和 重绘

回流（重排）：元素位置或大小发生改变，需要重新排列布局，例如元素的尺寸发生了改变、删除或增加了可见DOM

重绘：修改了元素的样式，但没有修改元素的尺寸和位置，例如 修改了元素的背景色、透明度

### 注意：回流一定会导致重绘，重绘不一定会触发回流

如何减少回流和重绘

-    放弃传统的`dom`操作，基于`vue/react`开始数据驱动视图
-   通过添加、移除类名来批量修改样式
-    元素的批量修改 ，例如有多个元素需要插入body中，应该使用文档对象的容器   `document.createDocumentFragment()` ，将需要插入的子节点添加到容器中，再一次插入到body中