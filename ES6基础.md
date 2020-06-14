## 块级作用域let和const

特点：

* 不会被声明提升，只是会进入暂时性死区，必须先定义再使用
* 重复声明会报错
* 不绑定全局作用域
* 会生成一个块级作用域
* 

循环中的let和const：

在循环中使用let后，每次循环会形成一个单独的作用域

```javascript
let data = [];
for(let i=0;i<3;i++) {
	data[i] = function(){
		log(i)
	}
}
data[0]() // 0

// 上面的代码相当于
// 伪代码
(let i = 0) {
    funcs[0] = function() {
        console.log(i)
    };
}
(let i = 1) {
    funcs[1] = function() {
        console.log(i)
    };
}
...
```





## 数组的扩展

##### 扩展运算符

扩展运算符，将一个数组，类数组，字符串，set，map等实现了iterator接口的对象转为用逗号分隔的参数序列，很像函数中rest参数的反面用法

```javascript
function test(){
    console.log(...arguments);     // 1,2,3
}
test(1,2,3)
```

主要用途：

```javascript
// 1、合并数组
let arr1 = [1,2];
let arr2 = [3,4];
let arr = [...arr1, ...arr2, 5]     //1,2,3,4,5

// 2、复制数组
// es5中只能用变通的concat()方法复制数组，在es6中可以通过扩展运算符解决这个问题
let arr1 = [1,2];
let arr2 = [...arr1];

// 3、与解构结合起来赋值
// 注：如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错
let [first, ...rest] = [1,2,3,4];    // first: 1, rest: [2,3,4]
let [first, ...rest] = [];     //first: undefined, rest: []

// 4、将字符串转为数组
let arr = [...'hello'];  // [ 'h', 'e', 'l', 'l', 'o' ]
```

##### `Array.from()`

用于将两类对象转为真正的数组，一是类似数组的对象，如`NodeList`, 函数参数等；另一类是实现了iterator接口的对象。

```javascript
// 类数组转为数组
let arrLike = {
	'0': 'a',
    '1': 'b',
    'length': '2'
}
// es5的写法
let arr = Array.prototype.slice.call(arrLike);
// es6的写法
let arr = Array.from(arrLike);  // [ 'a', 'b' ]

// arguments也是类数组
function fun() {
  var args = Array.from(arguments);
}

// 部署了iterator的对象转数组
Array.from('hello')    // [ 'h', 'e', 'l', 'l', 'o' ]
```

注： 扩展运算符背后调用的是遍历器接口（`Symbol.iterator`），如果一个对象没有部署这个接口，就无法转换。`Array.from`方法还支持类似数组的对象。所谓类似数组的对象，本质特征只有一点，即必须有`length`属性。因此，任何有`length`属性的对象，都可以通过`Array.from`方法转为数组，而此时扩展运算符就无法转换。

```javascript
Array.from({length: 2});  //[undefined, undefined]
```

`Array.from`的第二个参数是一个函数，类似数组的map方法，可以对每个元素进行处理

```javascript
Array.from([1,2,3], x => x * x);
// 相当于
[1,2,3].map(x => x * x);
```

##### 新添加的实例方法

```javascript
copyWithin(); 

find();  //参数是一个数组，在数组中查找符合要求的第一个元素，没有符合的选项返回undefined
let arr = [1,2,4];
let res = arr.find(function(item){
    return item > 2
})
console.log(res)  // 4

findIndex();  // 同find方法，只不过返回的是下标

fill();
entries();
keys();
values();

includes(); //检测是否包含一个指定的值
let arr = [1,2,3]
arr.includes(3)    //true

flat()
```



## 对象的扩展

##### 属性的简洁写法

经常用，不用多说了

##### 属性名表达式

属性名都是字符串，不管加不加引号。`es5`的对象字面量定义对象属性时，只能使用字符串，但是在`es6`中，可以使用[]定义

```javascript
let key = 'name';
let obj = {
    [key]: 'xiaoming',
    ['a' + 'ge']: 18
}
obj[key] === obj.name;
```

##### 属性的可枚举性和遍历

###### 可枚举性

对象的每个属性都有一个描述符descriptor，通过`Object.getOwnPropertyDescriptor`方法可以获得该属性的描述符对象，对象中的`enumerable`属性描述该属性是否可枚举，如果该属性为false，某些操作会忽略掉该属性，目前有四个操作会忽略：

* `for...in `循环
* `Object.keys()`
* `JSON.stringify()`
* `Object.assign()`

这四个操作中，`for...in`循环会遍历自身的和继承的所有enumerable为true的属性，其他三个只遍历自身的，不遍历继承的。

###### 遍历

属性的遍历目前有五种方法

1. `for...in`: 循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。
2. `Object.keys()`: 返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名。
3. `Object.getOwnPropertyNames()`: 返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。
4. `Object.getOwnPropertySymbols(obj)`: 返回一个数组，包含对象自身的所有 Symbol 属性的键名。
5. `Reflect.ownKeys()`: 返回一个数组，包含对象自身的所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。

##### 对象的扩展运算符

不止数组中有扩展运算符，对象也可以使用扩展运算符

```javascript
let a = {name: 'xiaoming'};
let b = {age: 18}
let c = {...a, ...b};    // {name: 'xiaoming', age: 18}
```

##### 对象新增的方法

1. `Object.is()`

   ```javascript
   // == 的缺陷： 会自动转换数据类型
   // ===的缺陷： NaN不等于自身，且+0不等于-0
   // 使用Object.is()方法，在所有环境中，只要两个值是一样的，那他们就相等
   ```

2. `Object.assign()`

   ```javascript
   // 用来合并数组，需要注意的点
   // 只可以浅拷贝，
   // 常用来为对象添加属性，方法，克隆对象，合并对象，为属性指定默认值等
   ```

3. `Object.getOwnPropertyDescriptors()`

   ```javascript
   // 返回对象自身所有属性（继承属性除外）的描述符对象
   const obj = {
       name: 'xiaoming',
       sayName: function(){
           console.log(this.name)
       }
   }
   Object.getOwnPropertyDescriptors(obj)
   // 
   { name:
      { value: 'xiaoming',
        writable: true,
        enumerable: true,
        configurable: true },
     sayName:
      { value: [Function: sayName],
        writable: true,
        enumerable: true,
        configurable: true } }
   ```

4. `Object.setPrototypeOf()`

   ```javascript
   // 设置对象的prototype
   let proto = {};
   let obj = {
       a: '1'
   }
   Object.setPrototypeOf(obj, proto);
   proto.b = '2';
   obj // {a: '1', b: '2'}
   
   // 相当于es5中
   let proto = {};
   let obj = {
       a: '1'
   }
   obj.__proto__ = proto;
   ```

5. `Object.getPrototypeOf()`

   ```javascript
   // 获取对象的__proto__属性
   function Rectangle() {
     // ...
   }
   const rec = new Rectangle();
   Object.getPrototypeOf(rec) === Rectangle.prototype
   ```

其他方法

```javascript
// 构造函数的部分方法
Object.create();  //创建一个新的对象，使用现有对象提供新对象的__proto__
const person = {
  isHuman: false,
  printIntroduction: function () {
    console.log(`My name is ${this.name}. Am I human? ${this.isHuman}`);
  }
};
const me = Object.create(person);
me.name = "Matthew"; 
me.isHuman = true; 
me.printIntroduction();    //"My name is Matthew. Am I human? true"

Object.defineProperty();
Object.defineProperties();
Object.keys();    //返回一个包含所有给定对象自身可枚举属性名称的数组。

Object.entries();  //返回给定对象自身可枚举属性的 [key, value] 数组
let obj = {
    a: '1'
}
console.log(Object.entries(obj))

Object.getOwnPropertyDescriptor();
Object.getOwnPropertyDescriptors();
Object.getOwnPropertyNames();    //返回一个数组，它包含了指定对象所有的可枚举或不可枚举的属性名。
Object.getOwnPropertySymbols();  //返回一个数组，它包含了指定对象自身所有的符号属性。

// 实例和原型对象方法
Object.prototype.hasOwnProperty();    // 返回一个布尔值 ，表示某个对象是否含有指定的非继承的属性
Object.prototype.isPrototypeOf();  // 返回一个布尔值，表示指定的对象是否在本对象的原型链中。
Object.toString();     //返回对象的字符串表示
```



## 函数的扩展

##### 函数参数的默认位置

##### `rest`参数

将多余的变量合并为一个数组

```javascript
let fun = function(a, ...rest){
    console.log(a, rest); // 1, [2,3,4]
}
fun(1,2,3,4)
```

注意： rest参数必须是最后一个

##### 箭头函数

1. 没有this对象和arguments参数，在函数中访问这两者的时候，其实是顺着作用域链找到的上级变量

2. 不可以当作构造函数，也就是说，不可以使用`new`命令，否则会抛出一个错误。

   这是因为函数有两个内部方法[[Call]] 和 [[Construct]]。当通过 new 调用函数时，执行 [[Construct]] 方法，创建一个实例对象，然后再执行函数体，将 this 绑定到实例上；当直接调用的时候，执行 [[Call]] 方法，直接执行函数体。箭头函数并没有 [[Construct]] 方法，不能被用作构造函数，如果通过 new 的方式调用，会报错。

3. 不可以使用`yield`命令，因此箭头函数不能用作 Generator 函数。

4. 没有原型prototype属性

5. 没有super，不过跟 this、arguments、new.target 一样，这些值由外围最近一层非箭头函数决定。

6. 不能通过bind, call, apply方法改变this的指向（已验证）

   ```javascript
   var name = 1;
   var fun = () =>{
   	console.log(this.name);
   }
   var obj = {name:2};
   console.log(fun.call(obj));  // 1
   ```

**关键点在于箭头函数没有自己的this, 他的this是继承自父执行环境, 如下代码**

```javascript
function person(){
    this.name = 'xiaoming';
    setTimeout(() => {
        console.log(this.name);
    }, 500);
}
let p =person();
// 从以上代码中可以看到，箭头函数中的this确实是继承自person, 如果person中的this.name = 'xiaoming'改成let name = 'xiaoming',那么会输出undefined。

class Dog{
    constructor(){
        this.name = 'dog'
    }
    getName() {
        return this.name;
    }
    // 这种方法在这里不可用，但是react中可以(此语法只建立在Babel中，不属于ES6语法内容)
    setName = () => {
        console.log('set');
    }
}

let d = new Dog();
let res = d.getName();
log(res);  // dog

// 上面代码中getName方法中的this, 就是Dog中的this。因此我想getName是用箭头函数实现的，但是自己实现setName方法时不可用。
```
**不适合使用箭头函数的场合**

定义对象的方法

```javascript
const cat = {
  lives: 9,
  jumps: () => {
    this.lives--;
  }
}
// 在这个方法中，由于对象不构成单独的作用域，因此this会指向全局对象。
```

需要动态this的时候

```javascript
var button = document.getElementById('press');
button.addEventListener('click', () => {
  this.classList.toggle('on');
});
// 上面代码运行时，点击按钮会报错，因为button的监听函数是一个箭头函数，导致里面的this就是全局对象。如果改成普通函数，this就会动态指向被点击的按钮对象。
```

## Set和Map

**set**

es6提供了新的数据结构，set和map。其中set类似于数组，但是它的值是唯一的。使用方法：

```javascript
// 新建
let set = new Set();
// 接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。
let set = new Set([1,2,3]);

// 实例属性和方法
Set.prototype.size()  //返回成员总数
Set.prototype.add(value)
Set.prototype.delete(value)
Set.prototype.has(value)
Set.prototype.clear()     //清除所有成员，没有返回值

// 遍历操作
Set.prototype.keys()
Set.prototype.values()
Set.prototype.entries()
Set.prototype.forEach()

// Set的遍历顺序就是插入顺序, 另外，keys方法和values方法返回值完全一样，因为set的键名和键值一样
let set = new Set(['red', 'green', 'blue']);

for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue
for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue

// 如果想在遍历操作中，同步改变原来的 Set 结构，目前没有直接的方法，但有两种变通方法。一种是利用原 Set 结构映射出一个新的结构，然后赋值给原来的 Set 结构；另一种是利用Array.from方法。
// 方法一
let set = new Set([1, 2, 3]);
set = new Set([...set].map(val => val * 2));
// set的值是2, 4, 6

// 方法二
let set = new Set([1, 2, 3]);
set = new Set(Array.from(set, val => val * 2));
// set的值是2, 4, 6
```

用途：

* 数组去重

  ```javascript
  // 数组去重方法
  let arr = [1,2,3,2,1]
  console.log([...new Set(arr)]);  // [1,2,3]
  
  //或者使用Array.form()方法
  Array.from(new Set(arr));
  ```

* 字符串去重

  ```javascript
  // 去除字符串的重复字符
  let str = 'hello';
  console.log([...new Set(str)].join(''))  // 'helo'
  ```

* 实现交集，并集，差集

  ```javascript
  let a = new Set([1, 2, 3]);
  let b = new Set([4, 3, 2]);
  let union = new Set([...a, ...b]);  //Set {1, 2, 3, 4}
  let intersect = new Set([...a].filter(x => b.has(x)));  //set {2, 3}
  let difference = new Set([...a].filter(x => !b.has(x)));  //Set {1}
  ```

**WeakSet**

weakSet类似set，不同之处在于，weakSet的子项都是对象；weakSet中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中。由于这个特点，WeakSet 的成员是不适合引用的，因为它会随时消失。另外，由于 WeakSet 内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后很可能成员个数是不一样的，而垃圾回收机制何时运行是不可预测的，因此 ES6 规定 WeakSet 不可遍历。这些特点同样适用于 WeakMap 。

weakSet结构的方法

```javascript
WeakSet.prototype.add(value)
WeakSet.prototype.delete(value)
WeakSet.prototype.has(value)

let obj = {}
let ws = new WeakSet();
ws.add(obj)
ws.has(obj)  //true
wa.delete(obj)
ws.has(obj)  //false
```

用处：

**Map**

javascript中的对象，本质上是键值对的集合，其中的键只能是字符串，为了使对象也可以成为键，es6提供了Map数据结构。使用方法：

```javascript
// 初始化
let map = new Map()
// 添加成员
let person = {name: 'xiaoming'}
map.set(person, 'content')
// 接受一个数组作为参数，该数组的成员是一个个表示键值对的数组。
const map = new Map([
    ["name", '张三', 'age', '19'],  // 后面的两个参数没有用
    ["address", '北京']
]);
for(let i of map){
    console.log(i)
}
// [ 'name', '张三' ]
// [ 'address', '北京' ]

// 也可以接受其他的具有iterator的，并且每个成员都是一个双元素的数组的数据结构作为参数，如set和map
const set = new Set([
    ['foo', 1],
    ['bar', 2]
]);
const m1 = new Map(set);
console.log(m1.has('bar'));  // true

const map = new Map([
    ["name", '张三']
]);
const m1 = new Map(map);
console.log(m1.has('name'));
```

注意点：

只有对同一个对象的引用，Map 结构才将其视为同一个键。这一点要非常小心。

```javascript
const map = new Map();

map.set(['a'], 555);
map.get(['a']) // undefined
// 上面的两个['a'] 是不一样的，因为它们的内存地址是不相同的。

const map = new Map();

const k1 = ['a'];
const k2 = ['a'];

map
.set(k1, 111)
.set(k2, 222);     //返回当前Map对象
map.get(k1) // 111
map.get(k2) // 222
```

实例的属性和操作方法

```javascript
size属性
const map = new Map();
map.set('foo', true);
map.set('bar', false);
map.size // 2

Map.prototype.set(key, value)   // 返回当前的Map对象，因此可以使用链式写法
Map.prototype.get(key)
Map.prototype.has(key)
Map.prototype.delete(key)
Map.prototype.clear()
```

遍历方法

```javascript
Map.prototype.keys()
Map.prototype.values()
Map.prototype.entries()
Map.prototype.forEach()
```

与其他数据结构的转换

```javascript
// Map转数组
const myMap = new Map()
  .set(true, 7)
  .set({foo: 3}, ['abc']);
[...myMap]   // [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]

// Map转对象
function strMapToObj(strMap) {
  let obj = Object.create(null);
  for (let [k,v] of strMap) {
    obj[k] = v;
  }
  return obj;
}
const myMap = new Map()
  .set('yes', true)
  .set('no', false);
strMapToObj(myMap)

// Map转json
function strMapToJson(strMap) {
  return JSON.stringify(strMapToObj(strMap));
}
let myMap = new Map().set('yes', true).set('no', false);
strMapToJson(myMap)
```



## PROXY

代理在计算机世界中挺常见的，比如代理模式，代理服务器，反向代理等等。代理的一般用途有：

1. 进行访问控制，如代理服务器管理真实服务器的访问权限，减少服务器压力
2. 增加功能，真实对象有些能力不足，需要代理服务器补足

##### `es6`中的代理

1. 代理的内容： `object`
2. 有什么用： 控制和修改`object`的基本行为
3. `object`的基本行为有哪些：属性调用，属性赋值，方法调用等

##### 用法

Proxy对象的所有用法，只有当做构造函数一种，不同的只是参数内容。如下：其中第一个参数是要代理的对象本身，可以是函数（函数也是对象）， 第二个参数是一个配置对象，对于每一个被代理的操作，需要提供一个对应的处理函数，该函数将拦截对应的操作。

```javascript
let handle = {
    get: function(obj, prop){
        if(prop === 'name'){
            return 'xiaoming'
        }else{
            return 'other'
        }
    }
}
let proxy = new Proxy({}, handle);
console.log(proxy.name)  // xiaoming
console.log(proxy.age)  // other
```

注意，要使得`Proxy`起作用，必须针对`Proxy`实例进行操作，而不是原对象（上例中为空对象）

proxy支持拦截的操作有十三种，主要：

```javascript
get(target, prop, receiver); // 拦截对象属性的读取，比如proxy.foo和proxy['foo']。
set(target, propKey, value, receiver); //拦截对象属性的设置，比如proxy.foo = v或proxy['foo'] = v，返回一个布尔值。
getPrototypeOf(target); // 拦截Object.getPrototypeOf(proxy)，返回一个对象。
apply(target, object, args); //拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
ownKeys(target); //拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)、for...in循环，
```

主要的用途：

```javascript
// 为属性设置符合需求的值
let handle = {
    set: function(target, prop, value){
        if(prop === 'age'){
            if(value < 0){
                throw new Error('年龄不能为负数');
            }else{
                target[prop] = value;
            }
        }
        return target[prop] = value;
    }
}
let proxy = new Proxy({}, handle);
proxy.age = -7;
proxy.name = 'xiaoming'
console.log(proxy);
// 上例中年龄不能为负数，如果设置了负数，在运行时会报错

// 下面这个例子中，本来是求最大值的方法， 通过proxy改成了求最小值
function fun(...rest){
    return Math.max(...rest);
}

let proxy = new Proxy(fun, {
    apply: function(target, ctx, args){  // 目标对象，目标对象的this, 参数数组
        return Math.min(...args);
    }
})
console.log(fun(1,2,3))  // 3
console.log(proxy(1,2,3))  // 1，
```



## REFLECT

`reflect`是一个对象，它和`proxy`一样，是为操作对象而提供的`api`, 包含的方法也和`proxy`的一样，设计它的目的，是为了规范对对象的操作，主要是以下几种

1. 将`Object`中一些明显属于语言内部的方法，如`Object.defineProperty`, 放到`Reflect`对象上。

2. 修改某些方法的返回结果，使其合理，如之前`Object.defineProperty()`在无法定义属性时，会抛出一个错误，而现在会返回`false`

3. 让`Object`的操作都变为函数形式，`in` , `delete`操作符分别由`Refelect.has()`, `Reflect.deleteProperty()`方法代替。

4. `Reflect`中的方法和`proxy`相对应，可以让`proxy`方便的调用，完成默认行为。

   ```javascript
   let proxy = new Proxy({}, {
       get: function(target, prop){
           console.log(`get ${prop}`);
           return Reflect.get(target, prop);
       }
   })
   proxy.name = 1;
   console.log(proxy.name);
   // get name
   // 1
   ```

##### Reflect中的静态方法

```javascript
Reflect.apply(target, thisArg, args);
Reflect.construct(target, args);

Reflect.get(target, name, receiver); //查找并返回target对象的name属性，如果没有该属性，则返回undefined。
let obj = {
    name: 'xiaoming'
}
console.log(Reflect.get(obj, 'name')) // xiaoming

Reflect.set(target, name, value, receiver)
Reflect.defineProperty(target, name, desc)
Reflect.deleteProperty(target, name)
Reflect.has(target, name)
Reflect.ownKeys(target)
Reflect.isExtensible(target); // 返回一个boolean值，表示当前对象是否可扩展
Reflect.preventExtensions(target); //让一个对象变得不可扩展
Reflect.getOwnPropertyDescriptor(target, name)

Reflect.getPrototypeOf(target); // 读取对象的__proto__属性，对应Object.getPrototypeof()
let obj = {
    name: 'xiaoming'
}
console.log(Object.is(Reflect.getPrototypeOf(obj), Object.prototype))

Reflect.setPrototypeOf(target, prototype); // 设置对象的__proto__属性
```



## PROMISE

简单来说，promise是一个容器，里面保存着未来将会发生的某个事件（一般是异步事件）的结果。从语法上来说，promise 是一个对象，从它可以获取异步操作的消息。

1. `Promise.prototype.then()`

   promise实例添加状态改变时的回调函数，返回一个新的promise对象，因此可以链式调用，而不必担心执行顺序。接受两个参数，第一个是resolved的回调函数，第二个是reject的回调函数。

   ```javascript
   getJSON('/post.json').then(function(data){
       return getJSON(data.url)
   }).then(function(data){
       console.log('resolved')
   }, function(err){
       console.log('err')
   })
   ```

2. `Promise.all()`

   该方法没有定义在原型对象上，而是作为构造函数的静态方法使用，接收一个数组参数，数组的每一项都是一个promise实例对象，如果数组每一项的状态都变成`fulfilled`，则它的返回值为每个单独项的返回值组成的数组。否则，只要有一项状态变为rejected，返回这一项的返回值。

   `Promise.all()`方法的效果实际上是「谁跑的慢，以谁为准执行回调」，那么相对的就有另一个方法「谁跑的快，以谁为准执行回调」，这就是`Promise.race()`方法。

3. `Promise.race()`

   接收的参数与all方法相同，只不过那一项的值先返回，整体值就是该项的返回值。

4. `Promise.resolve()`

   将现有对象转为Promise对象，如果参数是一个promise对象，则原封不动的返回。

   ```javascript
   Promise.resolve(1); 
   //等同于
   new Promise((resolve, rejected){
   	resolve(1);        
   })
   ```

## Symbol

1. 概述

   Symbol表示一个独一无二的值，使用的时候像调用函数一样即可, 但是不可以使用new操作符，我认为本质是调用Symbol函数之后返回一个唯一的字符串之类的东西。

   可以转为字符串或者boolean类型的值，但不可以和其他类型运算

   ```javascript
   let s1 = Symbol();
   let s2 = Symbol();
   s1 === s2  // false
   
   Boolean(s1)  //true
   s1 + '1' // TypeError
   ```

2. 常用的地方

   用作属性名：因为symbol生成的值是唯一的，因此用作属性名的时候，属性也是唯一的。

   消除魔术字符串：

   ```javascript
   const colors = {
       red: Symbol(),
       green: Symbol()
   }
   function handleColor(color){
       if(color === colors.red){
           console.log('red')
       }
       if(color === colors.green){
           console.log('green')
       }
   }
   handleColor(colors.red)
   ```

3. 注意事项

   `for...of`, `for...in`, `Object.keys()`, `JSON.stringify()`, `Object.getOwnPropertyNames()`等方法不会遍历到symbol属性，只有`Object.getOwnPropertySymbols()`和`Reflect.ownKeys()`方法可以遍历到

4. `Symbol.for()`, `Symbol.keyfor()`;

   ```javascript
   Symbol.for('foo') === Symbol.for('foo') // true
   Symbol('foo') === Symbol('foo')    //false
   
   let s1 = Symbol.for("foo");
   Symbol.keyFor(s1) // "foo"
   let s2 = Symbol("foo");
   Symbol.keyFor(s2)  // 未登记，返回undefined
   ```

   `Symbol.for()`不会每次调用都返回一个symbol类型的值，而是先检查给定的Key是否存在，如果不存在才会建立，否则返回已经存在的。`Symbol.for()`返回key

5. 内置的symbol值

   es6提供了11个内置的symbol值，可以看成唯一的一串字符串。举几个常见的

   `Symbol.hasInstance`: 对象的`Symbol.hasInstance`属性，指向一个内部方法。当其他对象使用`instanceof`运算符，判断是否为该对象的实例时，会调用这个方法。（感觉和proxy的某个方法很像，都是拦截默认操作的）

   ```javascript
   let MyClass = function(){
       this[Symbol.hasInstance] = function(foo) {
           return foo instanceof Array
       }
   }
   let s = [1, 2, 3] instanceof new MyClass()  // true
   ```

   `Symbol.iterator`: 指向该对象的默认遍历器方法。如下，obj本没有部署iterator接口，添加之后可以使用遍历方法了。

   ```javascript
   let obj = {
       [Symbol.iterator]: function* (){
           yield 1;
           yield 2;
           yield 3;
       }
   }
   for(let i of obj){
       console.log(i)
   }
   // 输出1, 2, 3
   [...obj]   // [1,2,3]
   ```




## Iterator和for ... of 循环

iterator是一个接口，部署了这个接口的数据结构，都可以被遍历。`es6`提供了新的遍历命令`for...of`循环，使用这个命令遍历某种数据结构时，该循环会自动寻找iterator接口。

默认部署iterator的数据结构有：

* Array
* Map
* Set
* String
* TypedArray
* 函数的arguments参数
* NodeList对象

`es6`规定，默认的iterator接口的实现定义在数据结构的Symbol.iterator属性上。以数组为例：

```javascript
let arr = [1,2,3,4];
let iter = arr[Symbol.iterator]();
console.log(iter.next());     //{value: 1, done: false}
```

那么这个接口具体是怎么实现的呢？

调用数据结构的Symbol.iterator方法，会返回一个指针对象，这个指针对象指向数据结构的第一个元素，调用next方法后，指向下一个元素，直到最后一个，大概实现如下。

```javascript
function iteratorDemo(){
  return {
    next: function(){
      return {value: 1, done: true}
    }
  }
}
let iter = iteratorDemo();
console.log(iter.next());  // {value: 1, done: true}
```

知道了它具体的实现之后，我们也可以给没有部署iterator接口的数据结构部署这个接口，比如对象默认没有部署iterator接口，但是我们可以自己添加，虽然意义不大

```javascript
const obj = {
  data: ['xiao', 'ming'],
  [Symbol.iterator]: function(){
    let self = this;
    let index = 0;
    return {
      next: function(){
        if(index < self.data.length){
          return {value: self.data[index++], done: false};
        }else{
          return {value: undefined, done: true}
        }
      }
    }
  }
}
for(let i of obj){
  console.log(i)
}
// 输出： xiao  ming 
```

##### for...of循环

引入这个循环，为的是有一个统一的遍历所有数据结构的方法，一个数据结构只要部署了`Symbol.iterator`属性，就被视为具有 iterator 接口，就可以用`for...of`循环遍历它的成员。也就是说，`for...of`循环内部调用的是数据结构的`Symbol.iterator`方法。没有的就不能调用，比如对象调用会报错。



## Generator函数的语法

Generator函数是一个状态机，内部有多种状态，执行函数后，返回一个遍历器对象，可以遍历函数内部的每一个状态。形式上，Generator函数和普通函数一样，但是有两个特征，其一是function关键字和函数名之间有一个*号；二是函数内部使用yield表达式，表示不同的内部状态，此外，return也可以表达状态。

调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，必须调用遍历器对象的`next`方法，使得指针移向下一个状态。也就是说，每次调用`next`方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个`yield`表达式（或`return`语句）为止。

运行逻辑如下：

（1）遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。

（2）下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。

（3）如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。

（4）如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

```javascript
function* gen(){
  yield 1;     // yield本身不返回值，或者说返回undefined
  yield 2;
  yield 3;
}

const iterator = gen() //返回一个遍历器对象
console.log(iterator.next())  //依次输出函数内部的状态

// for...of循环可以自动遍历 Generator 函数运行时生成的Iterator对象，且此时不再需要调用next方法。
// 因为for...of本身就是消费遍历器对象的
for(let item of iterator){      
    console.log(item)          
}

// 每次调用遍历器对象的next方法，就会返回一个对象，{value: value, done: false}。 对象中的value属性的值，即为状态机中的每个状态（即yield后面的表达式）。但是yield本身不返回值，可以给next添加一个参数，这个参数指定上一个yield表达式的返回值。
function* f() {
  for(var i = 0; true; i++) {
    var reset = yield i;
    if(reset) { i = -1; }
  }
}
var g = f();
g.next() // { value: 0, done: false }
g.next() // { value: 1, done: false }
g.next(true) // { value: 0, done: false }
```



## module模块

在es6之前，社区制定的模块加载方案有commonJS和AMD,前者用于服务器，后者用于浏览器。es6模块和commonJS模块之间有两个重大差异：

* CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
* CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

第二个差异是因为 CommonJS 加载的是一个对象（即`module.exports`属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。

`https://juejin.im/post/5aaa37c8f265da23945f365c`

另外：es6模块机制不缓存值，且值是只读的，不可改变，如果某个模块输出一个实例，那在引用这个模块的其他模块中该实例是共享的。

ES6 模块应该是通用的，同一个模块不用修改，就可以用在浏览器环境和服务器环境。为了达到这个目标，Node 规定 ES6 模块之中不能使用 CommonJS 模块的特有的一些内部变量。首先，就是`this`关键字。ES6 模块之中，顶层的`this`指向`undefined`；CommonJS 模块的顶层`this`指向当前模块，这是两者的一个重大差异。其次，以下这些顶层变量在 ES6 模块之中都是不存在的

- `arguments`
- `require`
- `module`
- `exports`
- `__filename`
- `__dirname`

