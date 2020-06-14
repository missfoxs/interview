###### `html`中的全局属性

下面的`html`代码中包含了大部分 全局属性，除了所有浏览器不支持的和常用的(他们会被单独列出来)

经常使用的全局属性`id`, `class`,`style`，不多介绍了。

大部分浏览器不支持的全局属性

`contextmenu`: 指定一个元素的上下文菜单。当用户右击该元素，出现上下文菜单，该属性只在`firfox`中有效.

`dropzone`: 指定是否将数据复制，移动，或链接，或删除

`translate`: 指定一个元素的值在载入页面时是否需要翻译

个人觉得需要注意的全局属性

`title`

`hidden`

`contenteditable`

`droggable`

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<meta http-equiv="X-UA-Compatible" content="ie=edge">
	<title>Document</title>
	<style>
	.title{
		font-weight: bold;
		color: brown;
	}
	</style>
</head>
<body>
	<h2>全局属性（所有元素都可使用的属性？）</h2>
	<p class="title">accesskey设置访问元素的键盘快捷键，在chrome下使用alt + h可聚焦到该输入框</p>
	<input type="text" accesskey="h">
	<a href="www.baidu.com" accesskey="c">百度</a>

	<p class="title">使用contenteditable属性指定元素是否可编辑,改变元素内容后，可以在js中获取改变后的值，对Input不生效</p>
	<div contenteditable="true" id="contet">可编辑的div <p>继承了父元素的可编辑属性</p></div>
	<button onclick="print()">打印可编辑div的值</button><br>
	<span id="contetValue"> </span><br>
	<input type="text" contenteditable="false">

	<p class="title">使用dir属性规定元素内容的文本方向,html5中新增，放在div中不起作用，在bdo标签中才有用</p>
	<bdo dir="rtl">这是从右向左的元素</bdo>

	<p class="title">使用draggable属性规定元素是否可拖动，图像和连接默认可以拖动</p>
	<div draggable="true">可拖动元素</div>
	<a href="#">链接默认可以拖动</a>

	<p class="title">使用hidden属性隐藏元素,效果和display:none一样，页面中找不到，但dom中有，也可以通过js找到</p>
	<div hidden id="hidden">被隐藏的元素</div>
	<div id="block">测试被隐藏元素是否还占位置</div>

    <p class="title">使用lang属性规定元素的语言,一般只在html标签中指定，浏览器发现指定的语言和浏览器本地语言不一致时会提示是否需要翻译</p>
	<div lang="fr">这里使用了法语</div>

	<p class="title">使用tabindex指定tab键导航顺序的链接</p>
	<div tabindex="2">1</div>
	<div tabindex="1">2</div>
	<div tabindex="3">3</div>

	<p class="title">规定元素的额外信息，可在工具提示中显示</p>
	<script>
		function print(){
			let dom = document.getElementById('contet');
			let val = document.getElementById('contetValue');
			val.innerText = dom.innerText;
		}

		let hiddenDom = document.getElementById('hidden');
		console.log(hiddenDom);
		let blockDom = document.getElementById('block');
		console.log(blockDom);
	</script>
</body>
</html>
```

###### `html5`中好用的标签

```html
	<p class="title">使用abbr标签标记需要注意的内容</p>
	<p><abbr title="投币，收藏，点赞">素质三连</abbr>是每位读者应有的态度</p>
	
	<p class="title">使用bdo设置内容方向</p>
	<bdo dir="ltr">上海自来</bdo>，水，<bdo dir="rtl">上海自来</bdo>
	
	<p class="title">使用mark标记内容</p>
	<p>五千六百万啊，四舍五入就是 <mark>一个亿</mark>啊</p>

	<p class="title">使用hr标签</p>
	<hr>

	<p class="title">使用meter标签</p>
	<p>c:盘<meter value="20" max="50">10G</meter><br /></p>
	
	<p class="title">使用progress标签显示进度条</p>
	<p>迅雷下载<progress value="0.599">99.9%</progress></p> 
	
	<p class="title">使用ruby标签注音</p>
	<p><ruby>福<rt>fu</rt></ruby>建人</p>
```