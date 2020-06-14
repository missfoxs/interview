##### 行内元素和块级元素的区别

1. 块级元素可以设置width，height，行内元素不可以设置该属性
2. 块级元素设置padding和margin对上下左右都起作用，行内元素只对左右起作用
3. 行内元素中只能有文本或其他行内元素

##### !DOCTYPE html的用途

指定浏览器以标准模式而非向后兼容的方式解析文档。

##### link和@import的区别

1. link是html的语法， @import是css语法
2. @import语句要写在文件的开始处
3. 加载页面时，link被同步加载，@import引入的元素在页面加载完成后才会被加载
4. 可以用js动态的插入link元素来改变样式，但不能对@import这样操作

##### iframe的缺点

1. 会阻塞页面onload事件的触发，因为要等到iframe中的元素加载完成后才会触发
2. 不利于SEO
3. 浏览器的退后按钮会失效

##### 常用的meta标签

指定编码的charset, 指定窗口的viewport, 页面描述，关键词，作者等

##### <pre>标签

预格式文本，保留文本原有的样式，如可以在其中显示代码

##### 什么是重绘和回流，以及减少回流的方式

重绘：元素的属性变化，但是这些属性只会影响外观，风格，而不会影响布局，比如background-color, color, visibility, border-radius, outline等，操作这些属性时发生的就是重绘

回流：会改变元素的尺寸，布局等的操作，如width， height, margin, padding, border, position, 还有修改网页的默认字体，计算offsetWidth等

减少回流的方式：

1. 为动画的 HTML 元件使用 fixed 或 absoult 的 position，那么修改他们的 CSS 是不会 reflow 的。
2. 千万不要使用 table 布局。因为可能很小的一个小改动会造成整个 table 的重新布局。
3. 不要一条一条地修改 DOM 的样式。与其这样，还不如预先定义好 css 的 class，然后修改 DOM 的 className。
4. 先将元素的display设置为none， 然后操作css样式，最后修改display

##### DOMContentLoaded 事件和 Load 事件的区别？

当初始的 HTML 文档被完全加载和解析完成之后，DOMContentLoaded 事件被触发，而无需等待样式表、图像和子框架的加载完成。Load 事件是当所有资源加载完成后触发的。我们在 jQuery 中经常使用的 $(document).ready(function() { // ...代码... }); 其实监听的就是 DOMContentLoaded 事件，而 $(document).load(function() { // ...代码... }); 监听的是 load 事件。

##### defer和async的区别

都是异步的加载js文件后执行，区别在于，async是加载完之后立即执行，defer是整个页面渲染完后再执行，还有就是async执行顺序和书写顺序不是对应的，而defer是对应的。

##### 浏览器的原理和渲染原理

`https://kb.cnblogs.com/page/129756/`

##### 什么是文档的预解析

当执行 JavaScript 脚本时，另一个线程解析剩下的文档，并加载后面需要通过网络加载的资源。这种方式可以使资源并行加载从而使整体速度更快。需要注意的是，预解析并不改变 DOM 树，它将这个工作留给主解析过程，自己只解析外部资源的引用，比如外部脚本、样式表及图片。

##### html语义化

1. 结构清晰，利于开发人员的维护
2. 利于搜索引擎的SEO
3. 没有css的情况下，也以一种文档格式显示