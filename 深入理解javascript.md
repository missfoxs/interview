今天看了github上深入理解js系列，觉得收获良多，但作者有些地方描述不是很精简，所以我还是自己记下笔记吧。

##### 原型到原型链

这部分作者写的反倒有些让人看不懂，我在interview里面记录的方法更好，这里就不展开了。



##### 词法作用域和动态作用域

`https://github.com/mqyqingfeng/Blog/issues/3`， 和后面的作用域链连起来看



##### 执行上下文栈

javascript中遇到可执行代码时（全局和函数），首先会有一个准备阶段，在这个阶段生成执行上下文，进入全局时生成全局上下文，上下文栈中压入全局上下文，进入函数时生成函数上下文，向栈中压入函数上下文，函数执行完毕时，从栈中弹出，这样上下文栈中始终有个保底的全局上下文。

上下文包含三部分，变量对象，作用域链，this



##### 执行上下文之变量对象

执行函数时，先进入准备阶段，此时生成激活对象，激活对象同变量对象一样，只不过是在正在执行的函数中的叫法，激活对象由arguments对象，参数，函数声明和变量声明组成，如下：

```javascript
function test(value) {
    function fn1(){};
    var v1 = 20;
    var fn2 = function(){};
}
test('val')
```

此时该函数的上下文如下，其中AO为激活对象

```javascript
testContext = {
	AO: {
		arguments: {
			0: 'val',
			length: 1
		},
        value: 'val',
        fn1: reference to function fn(){},
        v1: undefined,
        fn2: undefined
	},
    this: ...,
    作用域链：[AO, globalContext.VO]
}
```

等到函数执行时，会顺序执行代码，此时AO变为

```javascript
testContext = {
	AO: {
		arguments: {
			0: 'val',
			length: 1
		},
        value: 'val',
        fn1: reference to function fn(){},
        va: 20,
        fn2: reference to FunctionExpression fn2
	},
}
```

在这个过程中需要注意的点有

* 函数声明提升会提升整个函数，变量不会提升变量的值
* 函数声明比变量声明优先进行
* 如果函数声明和变量声明重复，以函数声明为主

先看下面两段代码的执行结果

```javascript
function test() {
	var fn;
	function fn(){};
	log(fn);    // [Function: fn]
}
test();

//
function test() {
	var fn = 1;
	function fn(){};
	log(fn);   // 1
}
test();
```

分析上面的执行结果，进入test方法生成变量对象过程中，先进行函数声明提升，有了变量fn，再声明变量fn时，会被忽略，但是第二个函数，执行阶段执行了fn = 1; 所以打印出1。

```javascript
function test() {
	var fn;
	function fn(){};
	log(fn);
}
// 代码片段1
var test = 2;
console.log(test);  //2
// 代码片段2
var test;
console.log(test); //[Function: test]
```





##### 执行上下文之作用域链

作用域链是由变量对象组成的，如下代码

```javascript
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope();
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}
checkscope()();
```

两段代码的执行结果都是`local scope`, 之前的解释都是，js是静态作用域，f函数定义在checkscope中，所以f函数不管在哪执行，都在checkscope作用域链下。但是现在知道了作用域链是由变量对象组成的，问题就来了，当执行f函数时，checkscope上下文已经从栈中弹出了，此时的上下文栈为：

```
[fContext, globalContex];
```

那为什么还是可以访问到checkscope中的活动变量呢？其实是作用域链和上下文栈是分开的，此时的作用域链为

```
[fContext.AO, checkscopeContext.AO, globalContext.VO],
```

可以看到即使checkscopeContext弹出，但是 JavaScript 依然会让 checkscopeContext.AO 活在内存中，f 函数依然可以通过 f 函数的作用域链找到它，正是因为 JavaScript 做到了这一点，从而实现了闭包这个概念。

**由此可以得出一个重要结论：访问箭头函数的this和arguments时，其实是访问的父函数的this和arguments**

```javascript
var a = 1;
var test = () => this.a;    //this是父函数，也即全局的this， agguments也是一样
console.log(test()); //1      
```





##### 执行上下文之this的指向

首先明确最重要的一点：当**函数被调用的时候**，**调用函数的那个对象**会被传递到执行上下文中，成为this的值。

可以将函数调用理解成下面的形式

```javascript
my_function.invoke(caller,arguments) 
```

调用者会作为参数被传递给invoke函数，**invoke函数负责创建执行上下文，然后开始执行函数代码**。

那么

```javascript
obj.fn(my_arg1, my_arg2)
```

就可以理解成

```javascript
fn.invoke(obj, [my_arg1, my_arg2])
```

结论：函数 this 的指向与这个函数定义在哪个对象中无关，只与调用这个函数的对象有关，this是在调用时确定的，而不是定义时。

从经验中总结的情形有下面几种

* 被对象调用，指向调用的对象
* 自主调用，则指向undefined， 在全局上下文this就指向window对象
* 构造函数中，指向生成的实例
* 如果是dom对象的调用，指向dom对象
* 通过apply或者call调用，指向第一个参数。
* 箭头函数不执行此规则

关于apply和call的方式调用，很像我们想象出的invoke方式。



##### 闭包

闭包就是父函数中返回的子函数可以访问父函数中定义的变量（不严谨），当执行到子函数的时候，父函数已经从上下文栈中弹出了，此时的上下文栈是`[子函数Context, globalContext]`, 但是`javascript`中依然会让父函数的VO(变量对象)存在于作用域链中，此时子函数的作用域链是`[AO, 父函数Context.VO, globalContext.VO]`。所有闭包才可以实现，这是特性，在其他语言如PHP中就不可以这样使用。

关于闭包有个很经典的面试问题，以前都是死记硬背，现在我终于知道是什么原因了

```javascript
let data = [];
for(var i=0;i<3;i++) {
	data[i] = function(){
		log(i)
	}
}
data[0]()
data[1]()
data[2]()
```

上面这段程序，定义变量`i`时如果使用`var`关键字，那么三个方法都会打印3， 如果用`let`关键字，则会打印0,1,2。为什么会出现这种情况呢? 分析一下

**使用var的时候**

当执行到`data[0]()`之前，此时全局上下文的VO为

```javascript
globalContext = {
    VO: {
        data: [...],
        i: 3
    }
}
```

当执行`data[0]()`的时候，`data[0]`函数的上下文为：

```javascript
data[0]Context = {
    Scope: [AO, globalContext.VO], 
    AO: {}
}
```

可以看到此时的作用域链为:`[AO, globalContext.VO]`，`data[0]Context`的 AO 并没有 i 值，所以会从 globalContext.VO 中查找，i 为 3，所以打印的结果就是 3。

**使用let的时候**

let会将代码块变为块级作用域，因此，相当于多了一层作用域链。在执行到`data[0]()`之前，全局上下文的VO不变，但是当执行`data[0]()`的时候，`data[0]`函数的上下文变为：

```javascript
data[0]Context = {
    Scope: [AO, 块级Context.VO, globalContext.VO]
}
```

此时会AO中，没有变量i, 但是块级作用域中有，因此会取到这个i的值。

**使用匿名函数**

也可以使用匿名函数模拟闭包，原理和使用let类型

```javascript
for (var i = 0; i < 3; i++) {
  data[i] = (function (i) {
        return function(){
            console.log(i);
        }
  })(i);
}
```

执行时，作用域链为: `[AO, 匿名函数Context.VO, globalContext.VO]`， 匿名函数中有i





##### 模拟实现call, apply

call的实现

特点：call 改变了 this 的指向

```javascript
function bar(name,age){
	return {
		value: this.value,
		name,
		age
	}
}
let foo = {
	value:2
}
Function.prototype.myCall = function(context) {
	var context = Object(context) || global;  //在node环境下为global, 浏览器中为window, 写Object是将基本数据类型包装为对象， 防止报错。
	context.fn = this;
	let arg = [];
	for(let i=1;i<arguments.length;i++){
		arg.push(arguments[i]);
	}
	let result = context.fn(...arg);
	delete context.fn;
	return result
}
// bar.myCall(null, 'xiaoming', 18); //传递null，this指向全局对象
let res = bar.myCall(foo, 'xiaoming', 18);
log(res);
```

apply方法，原理相同

```javascript
Function.prototype.myApply = function(context) {
	context = Object(context) || global;
	context.fn = this;
	let result = context.fn(...arguments[1]);
	delete context.fn();
	return result;
}
```





##### 模拟实现bind方法

特点，返回一个新函数，新函数的this是bind的第一个参数，如下

```javascript
var foo = {
    value: 1
};

function bar() {
    console.log(this.value);
}

// 返回了一个函数
var bindFoo = bar.bind(foo); 

bindFoo(); // 1
```

模拟实现

```javascript
Function.prototype.myBind = function(){
    let obj = arguments[0];
    let self = this;
    return function(){
        return this.apply(obj);
    }
}
```

这是最简单的实现方式，要考虑的特殊场景还有很多，如参数的处理，new的处理，详见`https://github.com/mqyqingfeng/Blog/issues/12`





##### 模拟new的过程

new的过程实现的效果主要有两个

1. 可以访问构造函数中的变量
2. 可以访问构造函数prototype的属性

```javascript
function Person(name, age) {
	this.name = name;
	this.age = age;
}
Person.prototype.color = 'black';
function objectFactory() {
	var obj = {};
	var cont = Array.prototype.shift.call(arguments);
	// 实例的 __proto__ 属性会指向构造函数的 prototype，也正是因为建立起这样的关系，实例可以访问原型上的属性。
	// obj.__proto__ = cont.prototype;
    // __protp__不是在所有环境中都有的，所有更好的实现方式如下
    function F(){};
	F.prototype = cont.prototype;
	obj = new F();
    // 使用apply给新对象添加构造函数中的属性
	cont.apply(obj, arguments);
	return obj;
}
let p = objectFactory(Person, 'xiao', 19);
log(p.color);
```

从上面的代码中可以看出，new一个对象的过程，分为以下四步

1. 创建一个新对象
2. 将新对象的`__proto__`指向构造函数的prototype， 这样就可以访问构造函数prototype的属性
3. 使用apply给新对象添加构造函数中的属性。
4. 返回新对象

另外，考虑到构造函数中的返回的情况，如果构造函数中返回值为对象，则返回这个对象，如果返回值为基本类型或者没有返回值，则返回我们创建的obj, 代码如下

```javascript
function objectFactory() {
    var obj = new Object(),
    Constructor = [].shift.call(arguments);
    obj.__proto__ = Constructor.prototype;
    var ret = Constructor.apply(obj, arguments);
    return typeof ret === 'object' ? ret : obj;
};
```



##### 创建对象的方法

1. 工厂模式
2. 构造函数模式
3. 构造函数模式和原型相结合



##### 继承的方式

1. 原型链继承， 缺点：父函数中引用的类型的变量会共享

   ```javascript
   function Parent () {
       this.names = ['kevin', 'daisy'];
   }
   
   function Child () {
   
   }
   
   Child.prototype = new Parent();
   
   var child1 = new Child();
   
   child1.names.push('yayu');
   
   console.log(child1.names); // ["kevin", "daisy", "yayu"]
   
   var child2 = new Child();
   
   console.log(child2.names); // ["kevin", "daisy", "yayu"]
   ```

2. 借用构造函数（经典继承）， 缺点：方法必须写在构造函数中

   ```javascript
   function Parent () {
       this.names = ['kevin', 'daisy'];
   }
   
   function Child () {	
       Parent.call(this);
   }
   
   var child1 = new Child();
   
   child1.names.push('yayu');
   
   console.log(child1.names); // ["kevin", "daisy", "yayu"]
   
   var child2 = new Child();
   
   console.log(child2.names); // ["kevin", "daisy"]
   
   ```

3. 组合原型链和构造函数继承

   问题：如下，Parent函数被操作了两次，分别在child构造函数中和新建prototype对象时，如果有打印日志等信息，会有问题
   
   ```javascript
   function Parent (name) {
       this.name = name;
       this.colors = ['red', 'blue', 'green'];
   }
   
   Parent.prototype.getName = function () {
       console.log(this.name)
   }
   
   function Child (name, age) {
       Parent.call(this, name);
       this.age = age;
   }
   
   Child.prototype = new Parent();
   Child.prototype.constructor = Child;
   
   var child1 = new Child('kevin', '18');
   
   child1.colors.push('black');
   
   console.log(child1.name); // kevin
   console.log(child1.age); // 18
   console.log(child1.colors); // ["red", "blue", "green", "black"]
   
   var child2 = new Child('daisy', '20');
   console.log(child2.name); // daisy
   console.log(child2.age); // 20
   console.log(child2.colors); // ["red", "blue", "green"]
   ```

4. 寄生组合式继承

   ```javascript
   // 寄生组合继承方法
   function Widget(width = '200px', height = '40px') {
       this.width = width;
       this.height = height;
       this.$el = null;
   }
   
   Widget.prototype.render = function($where){
       if(this.$el) {
           this.$el.style.width = this.width;
           this.$el.style.height = this.height;
           document.getElementById($where).appendChild(this.$el);
       }
   }
   
   // 定义Button组件
   function Button(width, height, label) {
       Widget.call(this,width, height);
       let el = document.createElement('button');
       el.innerText = label;
       this.$el = el;
   }
   
   Button.prototype = Object.create(Widget.prototype);
   
   //重新覆盖父类的render方法，添加onclick事件(多态)
   Button.prototype.render = function($where) {
       Widget.prototype.render.call(this, $where);
       // 这里有点不太明白， this指向到底是什么？？
       this.$el.addEventListener('click', this.onClick.bind(this));
   }
   
   Button.prototype.onClick = function() {
       // this.$el.style.backgroundColor = 'black'; // TypeError
       this.style.backgroundColor = 'black'; // error
       console.log('click')
   }
   
   let b1 = new Button('200px', '50px', 'click please');
   console.log(b1 instanceof Button);  // true 这里有点不太明白？？？
   console.log(b1 instanceof Widget);  // true
   b1.render('app');
   
   // 定义input组件
   function Input(width, height, value) {
       Widget.call(this, width, height);
       let input = document.createElement('input');
       input.value = value;
       this.$el = input;
   }
   
   // 不同于Button, 这里Input的constructor指向正确的位置
   let prototype = Object.create(Widget.prototype);
   prototype.constructor = Input;
   Input.prototype = prototype;
   
   let input = new Input('100px', '20px', 'xiaoming');
   input.render('app');
   ```

## 你不知道的Javascript的内容

### 对象

其实大部分东西都了解，只是一些细节的东西记不清了，在这里记录一下

##### 首先是属性描述符里面有几个基本的配置项

##### writeable:

1. 设置为false之后，属性值不能再修改，非严格模式下该属性还是原来的值，严格模式下报错TypeError

##### configurable：

1. 值为true时，表示该属性可以通过defineProperty()方法修改属性描述符。默认为true， 一旦修改为false，就会不可逆，此时再使用defineProperty()方法会报错TypeError
2. 除了无法修改，还会禁止删除该属性

##### enumerable：

1. 表示该属性是否可以出现在循环中，而不是是否能访问，具体来说如下

   直接访问属性，使用In操作符，使用obj.hasOwnproperty, 使用Object.getOwnpropertyNames等都可以访问到属性。

   使用for...in操作符，使用Objecty.keys()就不行，因为它们是枚举

##### [[GET]]:

1. 

##### [[put]]

1. 

#### 如果希望对象保持不变的话，有如下几种做法

1. 将一个属性的`writeable`和`configurable`设置为`false`，就可以创建一个真正的常量属性。

2. 使用`Object.preventExtensions()`方法，禁止一个对象添加新的属性

   ```javascript
   var obj = {
       a: 3
   }
   Object.preventExtensions(obj);
   obj.b=4;
   obj.b;  // undefined
   ```

3. 使用`Object.seal()`创建一个密封对象，实际上是在现有对象上调用`Object.preventExtensions()`方法，并将所有属性标记为`configurable: false`;

4. 使用`Object.freeze()`方法创建一个冻结对象，实际上是在现有对象上调用`object.seal()`方法，并将所有属性标记为`writable: false`;这样就无法修改他们的值，（属性如果是一个对象，那么需要深度冻结）

##### 对象的一些操作符



## 类型

##### 第一章

js中有6中基本数据类型(null, undefined, number, string, boolean, symbal)，1中引用类型Object，其中数组，函数，日期等都是Object的子类型。

在js中，undefined和undeclared（声明）是完全不同的，已在作用域中声明但还没有赋值的变量，是undefined 的。而还没有在作用域中声明过的变量，是 undeclared ，直接访问undeclared的值会报错。undefined和undeclared的变量使用typeof都会返回undefined。因此判断某个变量是undefined或undeclared时，可以使用typeof，这有时候会很方便。如下：

```javascript
// 在开发和测试阶段，全局中会有DEBUG模块
// 这样会抛出错误
if (DEBUG) {
	console.log( "Debugging is starting" );
}
// 这样是安全的
if (typeof DEBUG !== "undefined") {
	console.log( "Debugging is starting" );
}
```

##### 第二章

字符串和数组很相似，有许多相同的方法和属性，比如length, concat, indexOf等。但是数组的项是可以改变的，字符串是不可改变的，操作字符串时，总会返回一个新字符串。如果要使数组翻转，只需调用reserve方法即可，字符串翻转可以先转为数组，再调用翻转方法，最后变为字符串。

数字值可以使用Number类型封装，因此数字值可以调用Number.prototype上的方法；

```javascript
var a = 42.34;
a.toFixed(3); // "42.300"  //指定小数部分的显示位数
```

不过对于 . 运算符需要给予特别注意，因为它是一个有效的数字字符，会被优先识别为数字常量的一部分，然后才是对象属性访问运算符。

```javascript
42.toFixed(3); // SyntaxError;

42..toFixed(3); // "42.000"
```

数字0.1 + 0.2 == 0.3为false, 主要是因为二进制浮点数中的 0.1 和 0.2 并不是十分精确，它们相加的结果并非刚好等于0.3，解决方法在ES6中有个Number.EPSILON，他是一个数值，只需要将要对比的两个数相减的绝对值和该值比较，小于该值，说明近似相等

整数检测使用Number.isInterger()方法，42.0也是一个整数，使用该方法返回true。

如果数学运算的操作数不是数字类型，返回值为NaN，NaN意思是not a number，但这种说法其实有些问题，因为`typeof NaN === 'number'`, 说明NaN是“无效的数值”或“坏数值”。另外NaN不等于NaN。如果要判断NaN，可以使用全局的内置函数isNaN(), 然而这个函数也有一些问题，如下

```javascript
var a = 2 / 'foo';
var b = 'foo';
console.log(isNaN(a), isNaN(b));  // true, true
```

从上面可以看到，使用该方法时，会检查参数是否不是 NaN，也不是数字

从ES6开始，该问题可以使用Number.isNaN()方法进行解决

```javascript
if (!Number.isNaN) {
    Number.isNaN = function(n) {
    return (
            typeof n === "number" &&
            window.isNaN( n )
        );
    };
}
var a = 2 / "foo";
var b = "foo";
Number.isNaN( a ); // true
Number.isNaN( b ); // false——好！
```

特殊的数值除了NaN外，还有-0，判断这些特殊的值时，会有些问题，比如0 === -0, NaN != NaN都为true, 可以使用Object.is()方法解决，但尽量少用，因为他的效率不高。

值和引用，js中在赋值和传递参数时，如果是基本类型，则使用值赋值，如果是引用类型，使用引用赋值，且开发人员不能自行决定使用值赋值或引用赋值，一切由值的类型决定。JavaScript 中的引用和其他语言中的引用 / 指针不同，它们不
能指向别的变量 / 引用，只能指向值。

#### 第三章

所有使用typeof返回object的对象，都有一个内部属性[[Class]]，可以通过Object.prototype.toString方法来查看，一般情况，该属性和创建该对象的内部原生构造函数相对应，但不总是如此。基本类型中，null和undefined虽然没有原生构造函数，但内部的[[Class]]属性值依然为Null和Undefined，其他基本类型值为包装类型

```javascript
bject.prototype.toString.call( [1,2,3] );
// "[object Array]"
Object.prototype.toString.call( /regex-literal/i );
// "[object RegExp]"

Object.prototype.toString.call( null );
// "[object Null]"
Object.prototype.toString.call( undefined );
// "[object Undefined]"

Object.prototype.toString.call( "abc" );
// "[object String]"
Object.prototype.toString.call( 42 );
// "[object Number]"
Object.prototype.toString.call( true );
// "[object Boolean]"
```

使用new String('abc');创造出来的是字符串的封装对象，而不是基本类型值'abc'。对基本类型的值使用原生函数（如String）提供的方法时，引擎会自动将该值进行封装。

如果想要得到封装对象中的基本类型值，可以使用 valueOf() 函数。

```javascript
var a = new String( "abc" );
var b = new Number( 42 );
var c = new Boolean( true );
a.valueOf(); // "abc"
b.valueOf(); // 42
c.valueOf(); // true
```

创建数组时，不要创建空单元数组，何为空单元数组？

```javascript
var a = new Array(3);  // 在chrome下是[empty * 3];
var b = [undefined, undefined, undefined]  //[undefined, undefined, undefined]
```

上面的a就是空单元数组，他和undefined不一样。如果一定要创建一个指定长度的空数组，可以使用`Array.apply(null, {length: 3})`

#### 第四章    类型转换

可以看看这个`https://blog.csdn.net/itcast_cn/article/details/82887895`.

js中的类型转换分为两种，显示和隐式，当然都是相对的，在了解强制类型转换之前，需要理解几条规范，这几条规范定义了字符串，数字，和布尔值之间的转换规则。

* ToString

  定义了从非字符串到字符串的转换，基本类型值的字符串化规则为：null 转换为 "null"，undefined 转换为"undefined"，true转换为 "true"。数字的字符串化则遵循通用规则。对普通对象来说，除非自行定义，否则 toString()、返回内部属性 [[Class]] 的值，如[object Object]。但是如果对象自定义了toString()方法，将会调用对象自身的该方法，如数组的toString方法将所有单元字符串化以后再用 "," 连接后返回。

* ToNumber

  基本类型中， true 转换为 1，false 转换为 0。undefined 转换为 NaN，null 转换为 0，对字符串的处理基本遵循数字常量的相关规则 / 语法。处理失败时返回 NaN。

  对象（包括数组）会首先被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其强制转换为数字。为了将值转换为相应的基本类型值，抽象操作 ToPrimitive会首先检查该值是否有 valueOf() 方法。
  如果有并且返回基本类型值，就使用该值进行强制类型转换。如果没有就使用 toString()的返回值（如果存在）来进行强制类型转换。如果都没有，会产生TypeError错误。

* ToBoolean

  js定义了一些值为假值，假值的布尔强制类型转换结果为 false。这些假值包括如下

  * undefined
  * null
  * false
  * +0, -0, NaN
  * ""

  除此之外都为真值

##### 显示强制类型转换

###### 字符串和数字之间的类型转换，使用String()和Number(), 注意没有使用new关键字，不是创建对象，而是类型转换。

此外，还可以使用一元运算符+和-将字符串转为数字类型，-还会将结果取负

```javascript
var a = '3.14';
b = +a;   // 3.14
b = -a;    // -3.14  
```

###### 显示解析数字字符串

* 使用Number()转换
* 使用parseInf()解析

解析允许字符串中含有非数字字符，解析按从左到右的顺序，如果遇到非数字字符就停止，如果没有数字字符，返回NaN。而转换不允许出现非数字字符，否则会失败并返回 NaN。

```javascript
var a = '42';
var b = '42px';
Number(a);   // 42
Number(a);  // NaN
parseInt(a);  // 42
parseInt(b);  // 42
```

parseInt针对的是字符串，如果传入其他类型的值，会先转换为字符串，再进行解析

```javascript
parseInt(true);  //NaN  先转为"true";
parseInt(1); // 1, 先转为"1";
parseInt(['test', 2]);  //NaN  先转为"test, 2";
parseInt([1, 'test', 2]);  //1  先转为"1, test, 2";
```

特殊的例子

```javascript
parseInt(1/0, 19); // 18?
// 为什么会得到18呢？首先要明白，parseInt的第二次参数指定将字符串转换时的进制数，这里的19代表19进制。
// 其次第一个参数不是字符串而是一个表达式，1/0为"Infinity", 按照parseInt的解析规则，第一个字符是 "I"，以 19 为基数时值为 18， 第二个"n",不是一个有效字符，解析结束，返回18
parseInt( 0.000008 ); // 0 ("0" 来自于 "0.000008")
parseInt( 0.0000008 ); // 8 ("8" 来自于 "8e-7")
parseInt( false, 16 ); // 250 ("fa" 来自于 "false")
parseInt( parseInt, 16 ); // 15 ("f" 来自于 "function..")
parseInt( "0x10" ); // 16
parseInt( "103", 2 ); // 2
```

###### 显示转换为布尔值

和前面讲过的 + 类似，一元运算符 ! 显式地将值强制类型转换为布尔值。但是它同时还将真值反转为假值（或者将假值反转为真值）。所以显式强制类型转换为布尔值最常用的方法是 !!，因为第二个 ! 会将结果反转回原值，其次还有使用Boolean（）方法；

##### 隐式强制类型转换

###### 字符串和数字之间的隐式转换

使用数字运算符+操作时，如果某一个或者两个操作数都是字符串，那么 + 执行的是字符串拼接操作，这样解释只对了一半，其实是如果某个操作数是字符串或者能够通过以下toPrimitive转换为字符串的话，+ 将进行拼接操作

```javascript
[1] + [2];  // "12"
{} + {};  // NaN, 注意最后有分号，我猜测执行的是+ {};
{} + {}   // "[object Object][object Object]"
```

###### ToPrimitive方法

在操作符中，==， 加减乘除，排序运算符在对非原始值进行操作时，都会调用内部的toPrimitive方法，该方法接受两个参数，如下

```javascript
toPrimitive(input, type);
input是输入的值，type为期望转换的类型，可以是string或者number型，当type为number时，会执行以下步骤
1. 如果input是原始值，直接返回这个值；

2. 否则，如果input是对象，调用input.valueOf()，如果结果是原始值，返回结果；

3. 否则，调用input.toString()。如果结果是原始值，返回结果；

4. 否则，抛出错误。
如果type为string, 则2和3互换
```

`https://blog.csdn.net/weixin_44390947/article/details/86363723`

-,*,/会将两边的操作数转为number，再进行操作，如果不能转为数字，则为NaN

###### 隐式强制类型转为布尔值

下面的情况会发生布尔值隐式强制类型转换(很常见，没什么特殊的。。)

* if (..) 语句中的条件判断表达式
* for ( .. ; .. ; .. ) 语句中的条件判断表达式（第二个）
* while (..) 和 do..while(..) 循环中的条件判断表达式
* ? : 中的条件判断表达式
* 逻辑运算符 ||（逻辑或）和 &&（逻辑与）左边的操作数

以上情况中，非布尔值会被隐式强制类型转换为布尔值，遵循前面介绍过的 ToBoolean 抽象操作规则

&&和||

js中的逻辑运算符不同于java等语言，返回的不一定是布尔值，而是两个操作数中的其中一个。&&首先计算其左边的表达式，如果它的值为false或可被转换为false(null、NaN、0或undefined),那么将返回左边表达式的值，否则，它将计算右边的表达式， 并返回这个表达式结果作为 &&运算的结果。
||首先计算其左边的表达式，如果它的值不为false或不可被转换为false(null、NaN、0或undefined),那么将返回左边表达式的值，否则，它将计算右边的表达式， 并返回这个表达式结果作为||运算的结果。

```javascript
console.log(1 && 0);  // 0
console.log(0 && 1);  // 0
console.log(1 || 0);  // 1
console.log(0 || 1);  // 1
```

如果记不清的话，想想我们经常用的一种方式

```javascript
function foo(a,b) {
    a = a || "hello";
    b = b || "world";
    console.log( a + " " + b );
}
foo(); // "hello world"
foo( "yeah", "yeah!" ); // "yeah yeah!"

// 对于&&类型，我们也经常使用，叫守护运算符，即前面的表达式为后面的表达式把关
window && window.fun();
```

###### 抽象比较

==和===的问题

基本类型中，字符串，数字，布尔类型的判断，布尔和数字的判断，会将布尔转为数字再判断，布尔和字符串，会将布尔和字符串都转为数字进行判读，因此`true == '1'; true != '2'`, 数字和字符串的判断会将字符串转为（这里是转换而非解析）数字再判断。总结：都转为数字进行判断

```javascript
var a = '42';
var b = 42;
var c = false;
var d = true;
a == b;  // true, 这里是将a转为数字还是将b转为字符串再进行比较呢？答案是将a转为数字
a == d;  // false, 同上，将d转为数字，再进行比较，即为'42' == 1, 再将'42'转为42，结果为false.
a == c;  // false
// 从上可以得出结论，不要使用if(a == true); 这样的代码
```

null和undefined有特殊的规定，ES5规范规定，在 == 中 null 和 undefined 相等（它们也与其自身相等），除此之外其他值都不存在这种情况

```javascript
var a = null;
var b;
a == b; // true
a == null; // true
b == null; // true
a == false; // false
b == false; // false
a == ""; // false
b == ""; // false
a == 0; // false
b == 0; // false
```

对象和非对象之间的相等比较中，会将对象通过toPrimitive转换，然后和数字或字符串进行比较（没有布尔是因为如前所述，它被转为数字类型）

不常见的情况，如何让（a==2 && a==3）成立？a和基本类型比较时不可能同时等于2和3， 所以a只能是对象类型，对象类型和数字比较时，会使用toPrimitive方法转换，该方法又调用valueOf方法和toString方法，因此可以使用如下方式实现：

```javascript
// 重写toString或者valueOf方法，
var i = 2;
let a = {
	toString: function(){   // 这里换成valueOf方法也行
		return i ++;
	}
}
if(a == 2 && a==3) {
	log('it is posible');
}
// 重写valueOf方法
var i = 2;
Number.prototype.valueOf = function() {
	return i++;
}
let a = new Number();
if(a == 2 && a==3) {
	log('it is posible');
}
```

除了上面的几种方式外，还有很多其他特殊的，就不列举了。

### 第五章， 语法

执行一个语句（表达式）后，会有返回值，如delete obj.name; 如果成功返回true, 否则返回false，他的副作用是将obj中的name属性给删除了。再比如=运算符会返回值，副作用是将值赋给变量。多个赋值语句串联时，赋值表达式的结果值就能派上用场。

```javascript
var a = b = c = 3;
a; // 3
b; // 3
c; // 3
```

```javascript
[] + {}; // "[object Object]"
{} + []; // 0
// 第一个[]被转为"", {}被转为"[object Object]"
// 第二行代码，{}是一个代码块，而非值，因此实际执行的是 +[], 一元操作符+将[]转为0
```

##### 运算符优先级

```javascript
// ,运算符的优先级是最低的
var a = 42;
var b = a++, a;
b; // 42
// 加上括号之后
var b = (a++, a);
b; //43
```

&&和||会返回操作数中的某一个，但如果有两个运算符，三个操作数呢？答案是根据优先级，&&的优先级高于||。

```javascript
true && false || true   // true
true || false && true  // true
```

此外，|| 运算符优先级高于三元操作符? :

###### 关联

分为左关联（&&和||），右关联（？：， =）。决定同级运算符的执行顺序

```javascript
a && b && c;  // 实际为(a&&b) &&c;
a ? b : c ? d : e;  // 实际为a?b:(c?d:e);  如果是左关联，则为a ? b : (c ? d : e)
```

##### try...finally

即使在try中使用return返回，finally任然会执行。

## 第二部分

### 第一章， 异步

任何时候，只要把一段代码包装成一个函数，并指定它在响应某个事件（定时器、鼠标点击、Ajax 响应等）时执行，你就是在代码中创建了一个将来执行的块，也由此在这个程序中引入了异步机制。

JavaScript 引擎本身并没有时间的概念，只是一个按需执行 JavaScript 任意代码 片段的环境。“事件”（JavaScript 代码执行）调度总是由包含它的环境进行。所以，举例来说，如果你的 JavaScript 程序发出一个 Ajax 请求，从服务器获取一些数据， 那你就在一个函数（通常称为回调函数）中设置好响应代码，然后 JavaScript 引擎会通知 宿主环境：“嘿，现在我要暂停执行，你一旦完成网络请求，拿到了数据，就请调用这个 函数。”

setTimeout(..) 并没有把你的回调函数挂在事件循环队列中。它所做的是设定一个定时器。当定时器到时后，环境会把你的回调函数放在事件循环中，这样，在未来某个时刻的 tick 会摘下并执行这个回调。

**我的理解，javascript中的异步编程式这样的，程序分为现在执行的部分和将来执行的部分，现在执行的部分会立刻执行，但是将来的部分会被放在事件循环队列中（放到循环中的前提是宿主环境响应了这个事件，比如请求数据会在拿到服务端返回的数据后把将来执行的代码块放在循环中），在事件循环的时候被执行**

术语“异步”和“并行”常常被混为一谈，但实际上它们的意义完全不同。记住，异步是关于现在和将来的时间间隙，而并行是关于能够同时发生的事情。

竞态： 如两个ajax的回调函数都会影响程序中定义的某个变量，那这两个并发请求就是竞态。

```javascript
var a, b;
function foo(x) {
    a = x * 2;
    baz();
}
function bar(y) {
    b = y * 2;
    baz();
}
function baz() {
	console.log(a + b);	
}
// ajax(..)是某个库中的某个Ajax函数
ajax( "http://some.url.1", foo );
ajax( "http://some.url.2", bar );
// 最后打印的时候，不确定那个方法先执行，会影响打印的值，解决办法：
function foo(x) {
a = x * 2;
    if (a && b) {
   		baz();
    }
}
function bar(y) {
    b = y * 2;
    if (a && b) {
    	baz();
    }
}
```

###### 协作： 如果一个ajax请求返回100万条数据，要对这一百万条数据进行处理，如何做？

思路：处理时间太长会阻塞页面，用户体验不好，应该将数据放入数据循环中分批处理。

```javascript
var res = [];
// response(..)从Ajax调用中取得结果数组
function response(data) {
    var chunk = data.splice( 0, 1000 );
    res = res.concat(
        // 创建一个新的数组把chunk中所有值加倍
        chunk.map( function(val){
            return val * 2;
        } )
	);
    // 还有剩下的需要处理吗？
    if (data.length > 0) {
        // 异步调度下一次批处理
        setTimeout( function(){
            response( data );
        }, 0 );
    }
}
// ajax(..)是某个库中提供的某个Ajax函数
ajax( "http://some.url.1", response );
```

#### 任务

在 ES6 中，有一个新的概念建立在事件循环队列之上，叫作任务队列。

###### 编译语句重排序

javascript引擎会从上至下执行代码，但是在编译阶段，它会将代码（安全的）重排序，因为它觉得重排序之后执行速度会更快。



#### 回调地狱

1. 不符合大脑的思考方式，代码只能通过硬编码的方式书写，非常脆弱
2. 控制反转中的信任问题，回调函数过早触发，过晚触发，没有触发或者触发多次都是问题

分离回调设计：接受两个回调函数，一个用于成功通知，一个用于失败通知

永远异步调用回调，回调函数过早触发，很可能是同步的调用了回调函数，永远不要这样做。而要异步调用回调，即使就在事件循环的下一轮也好

```javascript
function b() {
    setTimeout(function(){
        console.log('b')
    },0)
}
function a(cb) {
    cb()
    console.log('a')
}
function c() {
    console.log('c')
}
a(b);
c() // a, c, b
```

#### promise

1. 如果程序中没有触发状态的变化，promise会永远处于pending状态

2. 状态只会触发一次，后续的都会被忽略

3. 如果使用return, 程序结束，状态仍处于pending

4. 实现链式调用的核心是每一个then都返回一个新的promise，并且会接受上一个的返回值（表述有问题，理解就好），但这里还有一个问题，如果某一个then中需要异步完成一些事情怎么办，这样我们不能直接return， 因为下一个then不会接受到该值，正确的做法是返回一个新建的promise，如下

   ```javascript
   var p = Promise.resolve( 21 ); 
   p.then( function(v){ 
       console.log( v )  // 21
       return new Promise(resolve => {
           setTimeout(()=> {
               resolve(33)
           },0)
       }) 
   }).then( function(v){ 
       console.log(v)  // 33
   })
   ```

5. 上面第四点说的这种类似于Promise.resolve()，Promise.resolve()方法会将传入的真正promise直接返回，对thenable进行展开

6. 每一个then方法的第二个参数都能捕获上层链上的错误