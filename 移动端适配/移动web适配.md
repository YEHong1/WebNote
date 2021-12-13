# 移动web端适配



## 1.先介绍几个相关概念

在CSS中我们一般使用`px`作为单位，需要注意的是，CSS样式里面的`px`和物理像素并不是相等的。CSS中的像素只是一个抽象的单位，在不同的设备或不同的环境中，CSS中的1px所代表的物理像素是不同的。在PC端，CSS的1px一般对应着电脑屏幕的1个物理像素，但在移动端，CSS的1px等于几个物理像素是和屏幕像素密度有关的。 



## 1.1  **Retina屏** （二倍屏）

Retina是一种显示标准，是把更多的像素点压缩至一块屏幕里，从而达到更高的分辨率并提高屏幕显示的细腻程度。 

## 1.2  **物理像素** 

物理像素又被称为**设备像素、设备物理像素**，它是显示器（电脑、手机屏幕）最小的物理显示单位，每个物理像素由颜色值和亮度值组成。所谓的一倍屏、二倍屏(Retina)、三倍屏，指的是设备以多少物理像素来显示一个CSS像素，也就是说，多倍屏以更多更精细的物理像素点来显示一个CSS像素点，在普通屏幕下1个CSS像素对应1个物理像素，而在二倍屏幕下，1个CSS像素对应的却是4个物理像素。

## 1.3  **设备独立像素** 

设备独立像素是我们写CSS时所用的像素（ `px` ），它是一个抽像的单位，主要使用在浏览器上，用来精确度量Web页面上的内容。 

## 1.4  **设备像素比** 

设备像素比简称为`dpr`，**物理像素与设备独立像素的比例**。

当这个比率为1:1时，使用1个设备像素显示1个`css`像素。当这个比率为2:1=2时，使用4 (2 * 2)个设备像素显示1个`css`像素。当这个比率为3:1=3，使用9 (3 *3)个设备像素显示1个`css`像素。

这里要注意，`dpr=2`,并不是物理像素是设备独立像素的2倍，而是用4个物理像素去表示1个设备逻辑像素 

 **`dpr`只代表一个数字比例，不是倍数关系。** 

![](E:\笔记\移动端适配\image\20181123093002592.png)

CSS的1px等于几个物理像素，除了和屏幕像素密度`dpr`有关，还和用户缩放有关系。例如，当用户把页面放大一倍，那么CSS中1px所代表的物理像素也会增加一倍；反之把页面缩小一倍，CSS中1px所代表的物理像素也会减少一倍。

## 1.5 **viewport**

viewport就是设备上用来显示网页的那一块区域，但viewport又不局限于浏览器可视区域的大小，它可能比浏览器的可视区域要大，也可能比浏览器的可视区域要小。 

再来了解一下三种视口：

-   可见视口 ：设备屏幕的宽度
-   布局视口：网页的宽度
-   理想视口：设置 布局视口 等于 可见视口，即为理想视口

移动端适配第一步就是把布局视口设置为可见视口的大小

```html
<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">

<!--
width: 设置layout viewport 的宽度，为一个正整数，或字符串”width-device”。

initial-scale: 设置页面的初始缩放值，为一个数字，可以带小数。

minimum-scale: 允许用户的最小缩放值，为一个数字，可以带小数。

maximum-scale: 允许用户的最大缩放值，为一个数字，可以带小数。

height: 设置layout viewport 的高度，这个属性对我们并不重要，很少使用。

user-scalable: 是否允许用户进行缩放，值为“no”或“yes”。
-->
```



## 2. 适配方案

## 2.1 rem适配

rem是相对于根元素的字体大小的单位，也就是html的font-size大小，浏览器默认的字体大小是16px，所以默认的1rem=16px，我们可以根据设备宽度动态设置根元素的font-size，使得以rem为单位的元素在不同终端上以相对一致的视觉效果呈现。 

```js
// 通过js 动态设置 rem
function remRefresh (){
    let htmlNode = document.documentElement;
    // 获取屏幕宽度
    const clientWidth = htmlNode.clientWidth;
    // 如果我们把 屏幕宽度 当做 1rem。那么我们在写css的时候写的就会全是小数
    // 屏幕宽度 / 10 是为了提高精确度 （js小数运算精度较差）
    const rem = clientWidth / 10;
    // 给html标签设置字体大小
    htmlNode.style.fontSize = rem + 'px';
    // 设置 body 的字体大小，这个其实就是设计默认字体大小，
    // 因为标签没有设置fontSize的情况下，会继承父元素的字体大小，例如 h1 就是 2em
    document.body.style.fontSize = '16px';
}

// 页面显示的时候计算 rem
window.addEventListener('pageshow', remRefresh)

// 屏幕尺寸改变时，重新计算 rem
let timer;
window.addEventListener('resize', ()=>{
    // 防抖
    if(timer){
        clearTimeout(timer)
    }
    timer = setTimeout(()=>{
        remRefresh()
    }, 1000)
})
```

这个文件我们不用自己，可以用淘宝的 `npm` 包 `lib-flexible`

使用例子:

750px的设计稿， 1rem = 75；设计稿中的div宽度为100px，那么在`css`中写 `width: 100 / 75 rem`

我们还可以用 `postcss-px2rem` 来配合使用，它的功能是将 `px` 转换为 rem，这个需要配合 `webpack` 使用



## 2.2  `vw`适配（不需要使用`js`）

VW表示的是视口的宽度(viewport width)，100vw = 视口的宽度

计算公式： （元素大小 / 设计稿大小）* 100vw 

以750px的设计稿为例， 

100vw = 750px，所以设计稿中宽度为 60px 的div，`css`可以写成 `width: 60 * 100/ 750 vw`











