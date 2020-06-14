#### 边框

使用border-image指定边框的图片或者使用渐变色

速记

```css
border-image: url(border.png) 30 30 round;
```

拆分

border-image-source:  用于指定要用于绘制边框的图像的位置

border-image-slice:   图像边界向内偏移

border-image-width: 图像边界的宽度

border-image-outset:用于指定在边框外部绘制 border-image-area 的量

border-image-repeat: 用于设置图像边界是否应重复（repeat）、拉伸（stretch）或铺满（round）。

#### 背景

给元素添加背景时，图片会以原始大小显示，但不会超出元素本身的大小，对背景图的操作一般包括指定大小：background-size、指定是否重复显示：background-repeat、指定图片位置：background-position,值为keyword,可以使用background-origin属性规定开始渲染的位置是否包含边框等。也可以直接使用background-clip规定。最后就是一个元素上有多个背景图，分别指定多个属性即可。

##### 包含的属性

* background-image

  通过此属性添加背景图片，使用`url`方式: `background-imgae: url(./paper.gif)`; 如果没有其他的属性，背景图就是原始图片的大小

* background-size

  在`css2`中，背景图像大小由图像的实际大小决定。现在可以通过此属性控制大小。

* background-repeat

* background-position

  值为left， top等关键字或者百分比。

* background-origin

  为background-position提供参考位置（以content, padding, 或者border开始渲染）。包含三个值`content-box`, `padding-box`和`border-box`。☞分别在content，padding，border中放置背景图片。如下，使用圆角的时候不会包含进去

  ![grid](D:\Workspace\Demos\components\css3\resimgs\background-position.png)

* background-clip

  值同background-origin，指定背景绘制区域。
  
  ![](D:\Workspace\Demos\components\css3\resimgs\background-clip.png)

##### 多背景

```css
background-image:url(img_flwr.gif),url(img_tree.gif);
background-position: right bottom, left top;
background-repeat: no-repeat, repeat;

或者
background: url(img_flwr.gif) right bottom no-repeat, url(paper.gif) left top repeat;
```

#### 渐变（也是背景的一种）

##### 线性渐变

```css
// 默认从上到下
background: linear-gradient(red, blue)
// 指定方向从左到右
background: linear-gradient(left, red, blue)
// 对角
background: linear-gradient(to right bottom, red, blue)
// 多个颜色
background: linear-gradient(red, blue, yellow, green, brown, cyan)
// 使用透明度
background: linear-gradient(to right, rgba(255,0,0,0), rgba(255,0,0,1));
```

##### 径向渐变

为了创建一个径向渐变，你也必须至少定义两种颜色结点。颜色结点即你想要呈现平稳过渡的颜色。同时，你也可以指定渐变的中心、形状（圆形或椭圆形）、大小。默认情况下，渐变的中心是 center（表示在中心点），渐变的形状是 ellipse（表示椭圆形），渐变的大小是 farthest-corner（表示到最远的角落）。 

```css
// 默认的径向渐变
background: radial-gradient(red, green, blue); 
// 指定位置(center)，形状(两种，默认为ellipse, 可以为circle)
background: radial-gradient(center, circle, red, green, blue); /* 标准的语法 */
```

#### 文本效果

text-shadow: 向文本添加阴影

text-overflow: ellipsis，clip 指定应向用户如何显示溢出内容

常与white-space: no-warp； overflow: hidden连起来使用white-space: no-warp表示文本不换行

text-wrap: 暂时所有浏览器都不支持

word-wrap: normal或者break-word, 在长单词或者`url`内部进行换行。换行是从下一行开始的（要换行的单词会被放在下一行）

word-break: normal或者break-all（允许在单词内换行，换行从本行开始）keep-all（只能在半角空格或者连字符处换行）

text-align: 除了常见的left, right ,center等取值外，还有个justify，表示两端对齐

text-align-last: 规定段落最后一行的对齐方式

#### 字体

好多还是不太明白

#### 过渡

原理就是用新加的class中的属性代替本来的属性，只不过加上一个过渡的时间

语法： `transition: width 2s, height 2s, transform 2s`

示例：

```css
.title{
	font-weight: bold;
	color: brown;
}
.box{
	width: 100px;
	height: 100px;
	border-radius: 10px;
	background-color: brown;
	padding: 10px;
	color: cyan;
	transition: all 2s;
}
.box1:hover{
	background-color: greenyellow;
	transform: rotate(360deg) scale(1.2, 1.2);
}
.box2{
    background-color: greenyellow;
    transform: rotate(360deg) scale(0.8, 0.8);
}

<h2>transition过渡</h2>
<p class="title">鼠标放上去后div过渡为另一种样式</p>
<div class="box box1">
    css transition
</div>

<p class="title">过一段时间后自动触发另一种样式</p>
<div class="box" id="box2">
	css transition
</div>
```

#### 动画animation

一般的`css`属性值都是固定的，animation的属性值是一个变量，要使用`@keyframes`指定，使用百分比指定每个阶段元素的样式即可。

需要注意的属性

animation-iteration-count: infinite; 指定动画播放的次数

animation-delay: 0; 规定动画开始的时间，默认为0

animation-direction: alternate; 规定动画在下周期是否逆向播放， 默认为normal

animation-timing-function; 默认为ease

linear:匀速

ease:慢速开始，加快，再慢速

ease-in:相对匀速，开始慢，之后快

ease-out开始快，之后慢

```
div{
	animation: 变量名
}

@keyframes 变量名{
	
}
```



```css
.title{
    font-size: 20px;
    color: brown;
}

.animation{
    width: 100px;
    height: 70px;
    border-radius: 5px;
    color: white;
    background-color: greenyellow;
    font-size: 20px;
    position: relative;
    animation: myanimation 3s;
    animation-direction: normal;
    animation-iteration-count: infinite;
}

@keyframes myanimation{
    0% {left: 0;}
    25% {transform: rotate(30deg); background-color: lightblue; left: 0;}
    50% {background-color: hotpink;left: 600px;}
    65% {left: 600px;transform: rotate(0);}
    100% {transform: rotate(-360deg); background-color: greenyellow; left: 0;}
}

<h2>css3动画</h2>
<p class="title">模拟动画首页</p>
<div class="animation">CSS3 动画</div>
```

#### 用户界面

包含三个属性

* resize:默认值为normal，用户不可改变元素大小，both: 允许改变高度和宽度，vertical:只允许改变高度，horizontal:只允许改变宽度

* box-sizing: content-box: 设置元素width时不包含padding和border，元素看起来会较大。 border-box: 设置元素width时包含padding和border，元素看起来会较小。在chrome默认为contentbox

* outline-offset: 指定元素的轮廓，和边框比较，轮廓不占用空间，也可以是非矩形

  ```css
  div
  {
  	margin:20px;
  	width:150px; 
  	padding:10px;
  	height:70px;
  	border:2px solid black;
  	outline:2px solid red;
  	outline-offset:15px;
  } 
  
  <div>这个 div有一个轮廓边界15 px边境外的边缘。</div>
  ```



### BFC格式化上下文

`https://www.jianshu.com/p/66632298e355`

### 层叠上下文‘

`https://blog.csdn.net/llll789789/article/details/97562099`