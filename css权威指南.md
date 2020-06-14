### 第六章  文本属性

主要介绍了对文本和文字的一些处理，常用的css属性如下

##### text-align

使用start代替left

##### text-decoration

使用该属性可以为文本添加下划线，删除线等

##### vertical-align

该属性只能用于行内元素或者置换元素，根据基线（父元素的底）来确定该元素的偏移量

#### word-spacing, letter-spacing

用来设置单词间距和字符间距

#### text-shadow

文本阴影

#### white-space

处理空白

* normal：默认
* nowrap: 不换行，会超出元素框的限制
* pre: 保留空格和换行符，不会把多个空格合成一个

#### word-break

处理断字

* break-all: 可以在任意地方换行，即使是单词内部
* keep-all: 禁止在字符之间换行

#### overflow-wrap, 原word-wrap

文本换行, 只有在white-space属性允许换行时才起作用3

* break-word:容许在单词内部换行，类似word-break: break-all

和word-break: break-all的区别

overflow-wrap：break-word只在有内容溢出时才换行，而word-break: break-all只要在内容接触边界时换行，不管前面有没有空白



### 第七章  视觉格式化基础