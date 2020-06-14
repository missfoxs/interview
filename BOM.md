### BOM(浏览器对象模型)

BOM的核心对象是window对象，他表示一个浏览器的实例，事实上，window对象具有双重身份，他既表示浏览器对象模型，也是js中的全局对象。BOM对象中的其他对象都是window对象的子对象。他们的关系如下:

###### window(bom)

* document
  * location
* location
* screen
* frames
* history
* navigator
* localStorage
* sessionStorage

从上可以看出location对象有些特殊，因为它既是window对象的属性，也是document对象的属性，即

`window.location === window.document.location`



###### window(js):

* 全局属性
  - infinity
  - NaN
  - undefined
* 全局方法
  * decodeURI()
  * decodeURIComponent()
  * encodeURI()
  * encodeURIComponent()
  * escape()
  * unescape()
  * eval()
  * isFinite()
  * isNaN()
  * Number()
  * parseInt()
  * parseFloat()
  * String()

##### window中常用的全局属性：

innerWidth, innerHeight, outerWidth, outerHeight

常用的全局方法：

setTimeout(), setInterval(), opne(), print()

```js
// open(url, name, specs, replace)方法有四个参数
// url: 可选，如果没有，打开一个空白页
// name: 可选，指定target属性，或窗口名称，属性有_blank, _self, _top, _parent,如果是窗口名称，则显示在
// 同名的iframe中
// specs： 可选，逗号分隔的列表，包含width，height等值，会根据此列表显示新的浏览器窗口，而非标签页，前提是
// 第二次参数为""
// replace

window.open('http://www.baidu.com', '', 'width=1000,height=500,left=10,top=10');
```

##### 其他location,screen,history,navigator和存储需要用的时候自查即可



##### History对象

History 对象包含用户（在浏览器窗口中）访问过的 URL。

包含的属性有: length;

包含的方法有: go(), back(), forward();



##### Location对象

包含当前url相关信息，包含的方法有hash, host, port,hostname等