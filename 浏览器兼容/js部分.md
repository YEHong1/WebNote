## 1.DOM的获取

 统一使用document.getElementById("idName"); 



## 2.const问题

IE浏览器只能使用 var 定义变量



## 3.event.x与event.y问题

IE： event对象有x和y属性，没有pageX和pageY属性

Firefox：event对象有pageX和pageY属性，没有x和y属性

兼容写法

```js
var x = event.x ? event.x : event.pageX;
var y = event.y ? event.y : event.pageY;
```



## 4.window.location.href问题

 Firefox1.5.x下,不支持window.location.href的写法。

所以修改网址，同意使用window.location



## 5.事件绑定

IE:dom.attachEvent();

其他浏览器：dom.addEventListener();

标准浏览器采用事件捕获的方式对应IE的事件冒泡机制（即标准由最外元素至最内元素或者IE由最内元素到最外元素）最后标准方亦觉得IE这方面的比较合理，所以便将事件冒泡纳入了标准，这也是addEventListener第三个参数的由来，而且事件冒泡作为了默认值。



## 6.ajax略有不同

IE：ActiveXObject

其他：xmlHttpReuest 



## 7.innerText在IE中能正常工作，但在FireFox中却不行.

需要使用 textContent 

```js
if(navigator.appName.indexOf("Explorer") > -1){
	document.getElementById('element').innerText = "my text";
} else{
	document.getElementById('element').textContent = "my text";
}
```







