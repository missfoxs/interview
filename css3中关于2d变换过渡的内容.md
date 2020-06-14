##### 关于`2d`和`3d`变换

###### 属性名：`transform`

属性值：`translate`位移、`scale`缩放、`rotate`旋转、`skew`倾斜、`matrix`矩阵（包含前面几个值）

建议将这几个单词背的滚瓜烂熟，这样就不用了每次用的时候都去找，也没几个。

在`2d`变换中,`translate`, `scale`, `skew`都有对应的在x轴和y轴上的方法，他们的参数也有两个,`rotate`只有一个，如下

```css
transform: translate(x, y)  // 位移的距离
transform: translateX(n)
transform: translateY(n)

transform: rotate(30deg) // 这里的参数是度数
transform: scale(1,1)  // 这里的参数是倍数
transform: skew(20deg, 20deg) // 度数
```

而在`3d`变换中,有x,y,z三个轴上的方法，但是`3d`变换没有`skew`

```css
transform:translate3d(x,y,z)
translateX(x)
translateY(y)
translateZ(z)

transform: rotate3d(x,y,z,angle) // xyz是0到1之间的值，表示在三个方向上的矢量
transform: scale3d(x,y,z)
```

###### 属性名： `transform-origin`

属性值：可以是数值+`px | em`, 百分比，`keywords`(`top`, `left`...)

在变换中旋转，缩放，倾斜的同时都可以使用该属性指定原点位置。但是位移`translate`始终以元素中心的位移，默认的原点为元素的中心点。元素的坐标轴的原点在元素的左上角，因此，默认的`transform-origin`值为`50%, 50%`; 如果想以左上角为中心进行旋转，倾斜的操作，`transform-origin`的值应该如下`0, 0`或者`left, top`

[transform-origin](http://caibaojian.com/transform-origin.html)

如果是在`3d`变换中，那他的参数有三个，第三个是距z轴的位置，可取的值只能是`length`。实际表现为元素向左移动的距离，这也是为什么取值只能是`length`的原因。



##### `translateZ`

朝着元素面向的方法移动的距离。在正六面体中，1号元素朝正面，使用此属性，元素会向正面移动，2号绕Y轴旋转180度后超背面，使用此属性朝背面移动，其他同理。



###### `transform-style`: 值为`flat`和`preserve-3d`. 指定图形以`2d`或者是`3d`形式展示

###### `perspective`: 指定视距的大小

##### 注意事项：

###### 父元素使用相对定位方法，在父元素上添加`perspective`和`transform-style`属性。

###### 子元素使用绝对定位方式，使用旋转等方法，指定他们的位置。

###### 一般在父元素上操作更多些，比如指定父元素的宽高，因为子元素绝对定位会脱离文档流。子元素只需指定位置即可。

###### 另外图形画成后可以使用`3d`旋转方法旋转整个元素