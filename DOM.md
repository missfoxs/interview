### DOM

##### document对象常用的属性和方法

document.addEventListener();      / /向文档添加句柄

document.cookie;       // 设置或返回cookie

documet.createElement/craeteAttribute/createTextNode      //创建元素/属性/文本节点

document.lastModified()            // 最后一次修改的时间 



###### dom元素对象

常用方法： appendChiled(), addEventListener();

常用属性： attributes,  childNodes,  calssName, calssList,



###### js事件中的this和event

在`html`中内嵌javascript的事件时，可以传递this和event两个参数，他们之间有什么区别呢？

this是指触发事件的元素，在javascript中获取到这个元素之后，可以将它看出普通的元素进行操作。

```javascript
<div onclick="testEvent(this)" id="smile">点击</div>

function testEvent(element){
    console.log(element.id);     //smile
    element.innerHTML = '改变元素的显示内容';
}

也可以如下直接在this中传递需要用的值
<div onclick="testEvent(this.id)" id="smile">点击</div>
```

event对象是事件对象，即使不传递这个值，在大部分浏览器中也可以用window.event获取到

```javascript
<div onclick="testEvent(event)" id="smile">点击</div>

function testEvent(e){
    console.log(e);   // event对象
}
```

上面代码中event包含的常用属性和方法：target: 触发此事件的元素，preventDefault(): 阻止默认的事件，clientX: 事件触发时，鼠标指针的水平位置，screenX:	同clientX, 区别在于screen针对整个显示器的屏幕，client针对浏览器可视区域，不随页面滚动而改变。此外，还有pageX, layerX等确定位置的属性，具体[点这儿](<https://segmentfault.com/a/1190000002405897>)。



