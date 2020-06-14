### CLASS

定义类的方法时，不需要添加`function`关键字，方法之间也不需要逗号分隔

类的数据类型就是函数，类本身就指向构造函数

```javascript
class Point{
    constructor(x,y){
        this.x = x;
        this.y = y;
    }

    toString(){
        return this.x + this.y;
    }
}
log(typeof Point)   // function
log(Point.prototype.constructor === Point)   //true
let p = new Point(1,2);  
log(p.toString());  //3
```

类的所有方法，都定义在原型prototype上，基本上类就是es5中方法的语法糖，类的构造函数中的属性即为实例的属性，类的方法为prototype的方法。

类中定义的所有方法，都是不可枚举的，这点和es5不一致

类和模块的内部，默认就是严格模式

与es5一样，在类的内部也可以使用set和get关键字，对某个属性设置存值和取值，拦截该属性的存取行为，且这两个函数设置在descriptor上

```javascript
class Point{
    constructor(x,y){
        this.x = x;
        this.y = y;
    }
    set name(name){
        log('setName: ' + name);
    }
    get name(){
        return 'xiaoming'
    }
}
let p = new Point(1,2);
p.name = 1;  // setName: 1,
log(p.name);  // xiaoming
let res = Object.getOwnPropertyDescriptor(Point.prototype, 'name');
log("get" in res);  //true
log("set" in res); //true
```

类也可以写成表达式的形式，如下代码，但是需要注意的是，这个类的名字是Me, 只能在类的内部使用，不能再用来新建对象了，在外部只能使用`myClass`引用类。

```javascript
const myClass = class Me{
    getClassName(){
        return Me.name
    }
}
let m = new myClass();
log(m.getClassName()); // Me
// let m = new Me()     //报错
```

不存在变量提升

**this的指向**

类的方法中的this,默认指向类的实例，单独提出来使用的话，很可能报错。如下代码，单独使用的时候，this会指向当前环境，由于class内部是严格模式，因此这里的this就是undefined。

```javascript
class myClass{
    constructor(){
        this.name = 'xiaoming';
    }
    getName(){
        return this.name
    }
}
let m = new myClass();
let {getName} = m;
log(getName());  // 报错
```

解决办法是使用箭头函数，或者在constructor中绑定this对象。

```javascript
class myClass{
    constructor(){
        this.name = 'xiaoming';
        this.getName = this.getName.bind(this)  //绑定this
    }
    getName(){
        return this.name
    }
}
let m = new myClass();
let {getName} = m;
log(getName());  // xiaoming

// 使用箭头函数
class myClass{
    constructor(){
        this.name = 'xiaoming';
        this.getName = ()=>{
            return this.name;
        }
    }
}
let m = new myClass();
let {getName} = m;
log(getName());
```

**静态方法**

如果在类的一个方法前加上static关键字，表示这是一个静态方法，该方法不会被实例继承，只能通过类来调用，静态方法中如果包含this，那么这个this指的是类本身，而不是实例。另外，父类的静态方法可以被子类继承。

实例属性除了写在constructor中之外，也可以写在类的顶层，这时候就不用加this了，但是很多地方都没有实现该方法。

**静态属性**

目前实现静态属性的方法只有一种，如下

```javascript
class Foo{}
Foo.name = 'xiaoming'

// 有个新提案，可以像静态方法那样写属性
class Foo{
    static name = 'xiaoming'
}
```

**私有属性和方法**

es6没有提供，只能使用变通的方法，方法1：在命名上加以区分，比如采用下划线，但这种方式依然可以被外部访问。

方法2：将私有方法移出去

```javascript
class Widget {
  foo (baz) {
    bar.call(this, baz);
  }

  // ...
}

function bar(baz) {
  return this.snaf = baz;
}
```

方法3：使用symbol

```javascript
const bar = Symbol('bar');
const snaf = Symbol('snaf');

export default class myClass{

  // 公有方法
  foo(baz) {
    this[bar](baz);
  }

  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }

  // ...
};
```

目前有个提案，在私有属性和方法前加#。

**new.target属性**

new是从构造函数生成实例对象的命令，es6为new命令引入一个new.target属性，一般用在构造函数中，返回new命令作用于的那个函数，如果构造函数不是通过`new`命令或`Reflect.construct()`调用的，`new.target`会返回`undefined`，因此这个属性可以用来确定构造函数是怎么调用的。

```javascript
function Person(name) {
  if (new.target === Person) {
    this.name = name;
  } else {
    throw new Error('必须使用 new 命令生成实例');
  }
}

var person = new Person('张三'); // 正确
var notAPerson = Person.call(person, '张三');  // 报错
```



## 继承

es6中的继承方法

```javascript
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }
}

class ColorPoint extends Point {
    constructor(x, y, color){
        super(x, y);
        this.color = color;
    }
    toString(){
        return {x: this.x,y: this.y,color: this.color}
    }
}

let cp = new ColorPoint(1,2,'red');
log(cp instanceof Point);
log(cp instanceof ColorPoint);
```

如上代码中，子类必须在constructor中调用super()函数，这是因为子类自己的`this`对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用`super`方法，子类就得不到`this`对象。

ES5 的继承，实质是先创造子类的实例对象`this`，然后再将父类的方法添加到`this`上面（`Parent.apply(this)`）。ES6 的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到`this`上面（所以必须先调用`super`方法），然后再用子类的构造函数修改`this`。

最后，父类的静态方法，也会被子类继承。

**super关键字**

有两种用法，作为函数和作为对象

作为函数时，只能在构造函数中使用，代表父类的构造函数，但是返回的是子类的实例。即`super`内部的`this`指的是子类的实例。

```javascript
class A {}

class B extends A {
  constructor() {
    super();     // 这里的super相当于A.prototype.constructor.call(this)
  }
}
```

作为方法时，指向父类的原型对象；但是在静态方法中，指向父类。

```javascript
class A {
    constructor(){
        this.name = 'xiaoming'
    }
    p() {
      return 2;
    }
  }
  
  class B extends A {
    constructor() {
      super();
      console.log(super.p()); // 2
      console.log(super.name);  // undefined  super指向父类的原型对象，所以定义在父类实例上的方法或属性，是无法通过super调用的。
    }
  }
  
  let b = new B();
```

**类的prototype和 [[ proto ]]**

