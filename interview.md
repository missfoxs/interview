1. 项目经验

   难点：添加`lastSessionId`

2. `vue`基础知识，原理的考察

3. `vue`全家桶

4. `javascript`中的常用内容考察，如原型，闭包，继承，箭头函数等等

5. `es6`新语法的考察

6. `html5`和`css3`的内容

7. 设计模式，算法，数据结构等

8. `webpack`等开发工具

9. `node`或者`java`等后端语言

10. 性能优化，前端安全

11. websocket

#### base

1. 对缓存的理解

   见缓存章节

2. 对`https，http2`的理解

   `https://segmentfault.com/a/1190000019891825`

   http2: 服务器推送，首部压缩，多路复用，流量控制

3. 对原型链的理解，画一个原型链图

   1. 对象有`__proto__`属性，函数有`prototype`和`__proto__`属性，其中`prototype`是函数专有的

   2. 对象由函数生成，(新建一个对象时，相当于使用了new Object， 新建一个函数，相当于new Function)

   3. 生成对象（这里的对象包括函数）时，对象的`__proto__`指向生成这个对象的函数的prototype函数

   4. `__proto__`只能在学习和调试下使用

   解释一下第三条

   ```javascript
   // 创建空对象时，相当于使用Object函数生成的
   var o = {}
   o.__proto__ === Object.prototype     //true
   
   // 使用函数创建自定义对象时，上面的规则同样适用
   var myObj = function(){}
   var mo = new myObj();
   mo.__proto__ === myObj.prototype     // true
   
   // 将函数作为对象看待，同样适用这个规则，只不过函数是由Function生成的
   let fun = function(){}
   fun.__proto__ === Function.prototype;    // true
   
   // Function， Object函数本身也适用这一规则，Object函数也是函数，所以由Function生成
   Function.__proto__ === Function.prototype;
   Object.__proto__ === Function.prototype;
   
   //那么prototype又是什么呢
   /*
   一般函数默认的prototype是一个类型为"object"的对象，它有两个属性：constructor和 __proto__。其中constructor属性指向这个函数自身，__proto__属性指向Object.prototype，这说明一般函数的prototype属性是由Object函数生成的。
   */
   
   // 上面讨论的都是一般情况，特殊情况下
   // Object函数的prototype上没有__proto__， 打印出来是null
   // Function函数的prototype不是一个object类型对象，而是一个function类型的对象，
   // 打印出来是ƒ () { [native code] }. 其中的__proto__是Object的prototype。
   console.log(Function.prototype.__proto__ === Object.prototype)   // true
   // https://www.jianshu.com/p/686b61c4a43d
   ```

5. 箭头函数和正常函数的区别

   1. 去掉function关键字，如果只有一个参数，可以省略圆括号，如果函数体只是一个return语句，可以省略方括号
   2. 不会创建自己的this, 只会从作用域链的上一层继承（定义对象的{}无法形成一个单独的执行环境）
   3. call, apply, bind无法改变箭头函数中this的指向
   4. 不能作为构造函数
   5. 没有自己的arguments
   6. 没有原型prototype

   `https://juejin.im/post/5c979300e51d456f49110bf0#heading-8`

6. `class`的实现，用原型写一个继承

7. `ajax, fetch, axios`的区别

   `ajax`是对原生`xhr`的封装，使用起来很方便，但我觉得他有一些小问题，首先是很多时候我们只想用`ajax`,但是必须要引入`jquery`，这非常的不友好，其次是不支持`promise`写法，最后逼格不够高。

   `axios`，也是对原生`xhr`的封装，但是他比`ajax`更好的地方就在于解决了上述说的`ajax`的两个问题，另外，他还支持并发请求的接口，拦截和转换请求与响应，也可以取消请求，自动转换json数据，客户端支持防止CSRF。

   ```javascript
   function getUserAccount() {
     return axios.get('/user/12345');
   }
   
   function getUserPermissions() {
     return axios.get('/user/12345/permissions');
   }
   
   axios.all([getUserAccount(), getUserPermissions()])
     .then(axios.spread(function (acct, perms) {
       // Both requests are now complete
     }));
   ```

   `fetch`不是ajax的封装, 也没有使用XMLHttpRequest，是原生js。 存在的问题是只对网络请求错误做reject，如返回404，500都会resolve；不会带默认的cookie，需要添加配置项；没有办法原生检测请求的进度。

9. webpakc的打包原理

   `https://github.com/Pines-Cheng/blog/issues/45`

   原理大概就是首先读取配置文件，然后从入口文件开始，读取文件并且分析模块依赖，然后通过loader和plugin对文件进行解析执行。最后生成bundle文件。

   打包后的文件简略如下：

   ```javascript
   (function(modules) {
     // 缓存 
     installedModules = []
     // 模拟 require 语句
     function __webpack_require__() {
     }
     // 执行存放所有模块数组中的第0个模块
     __webpack_require__(0);
   
   })([/*存放所有模块的数组*/]);
   ```

10. webpack中的异步加载

    webpack配置中需要在output字段添加chunkFileName,指定非入口chunk的名称，然后在要触发异步加载的地方（页面跳转或者某个按钮被点击的监听函数中时）使用import()方法导入模块即可。

    ```javascript
    window.document.getElementById('btn').addEventListener('click', function () {
      // 当按钮被点击后才去加载 show.js 文件，文件加载成功后执行文件导出的函数
      import(/* webpackChunkName: "show" */ './show').then((show) => {
        show('Webpack');
      })
    });
    ```

    代码中最关键的一句是 `import(/* webpackChunkName: "show" */ './show')`，Webpack 内置了对 `import(*)` 语句的支持，当 Webpack 遇到了类似的语句时会这样处理：

    - 以 ./show.js 为入口新生成一个 Chunk；
    - 当代码执行到 import 所在语句时才会去加载由 Chunk 对应生成的文件。
    - import 返回一个 Promise，当文件加载成功时可以在 Promise 的 then 方法中获取到 show.js 导出的内容。

11. webpack中loader和plugin的区别，以及如何实现

    loader是对文件本身的操作，比如他会将scss文件转为css文件，处理一个文件可以使用多个loader。

    在webpack运行的生命周期中会广播出许多事件，plugin可以监听这些事件，在合适的时机通过webpack提供的API改变输出结果，它丰富了webpack本身，针对是loader结束后，webpack打包的整个过程，它并不直接操作文件，而是基于事件机制工作，会监听webpack打包过程中的某些节点，执行广泛的任务

12. webpack中的代码分割

    `https://www.jianshu.com/p/c42a817dc8cf`

    1. 指定多入口文件（如果每个入口中都使用了同一个模块，会被重复加载，解决方案就是CommonsChunkPlugin）
    2. CommonsChunkPlugin
    3. 前面提到的异步加载

13. 对promise`的理解，如何实现一个`promise

    多个then函数时，如果某一个then方法错误，后面的then会接着执行吗？不会

    then方法如何给下一个then传递参数？ 直接返回return即可

14. 闭包，作用域的理解

    见深入理解javascript系列

    * 可以读取其他函数内部变量的函数，在`javascript`中只有子函数才能取得函数内部变量
    * 作用：在外部访问函数内的变量（模块，访问模块内部方法和属性），让变量保存在内存中，封装对象的私有属性和私有方法，模块化

    ```javascript
    // 访问内部变量
    function outer(){
        let n = 1;
        return function inner(){
            return n;
        }
    }
    let res = outer()();  // 1
    
    // 保存内部状态, 可以用来做计数器
    // 原理： 正常函数运行完后会在内存中释放。但这里inc始终在内存中，inc又依赖函数createIncrementor，因此它也不会在内存中消失。
    function createIncrementor(start){
        return function(){
            return ++ start;
        }
    }
    let inc = createIncrementor(0);
    log(inc());  //1
    log(inc());  //2
    log(inc());  //3
    
    //封装对象的私有属性和方法
    // 
    function Person(){
        let _name;
        function setName(name){
            _name = name;
        }
        function getName(){
            return _name;
        }
        return {
            name: _name,
            setName,
            getName
        }
    }
    let p = Person();
    p.setName('xiao')
    log(p.name, p.getName())  // undefined, 'xiao'; z这里p.name为什么是undefined? 通过闭包，变量_name变成了p的私有变量，如果给_name添加一个初始值，这里就会输出初始值。
    ```

15. 跨域请求可以携带`cookie`吗

    如果将token保存在cookie中，使用axios请求时，默认是不带cookie的，axios提供两种解决方案：

    * 添加自定义请求头

      ```javascript
      axios.get(url, {
      	headers: {
              token: TOKEN
          }
      })
      ```

      这种方式会引发options请求，需要后端过滤掉

    * 启用跨域时需要使用凭证

      ```javascript
      axios.get(url, {
      	withCredentials: true
      })
      ```

      但是这种方式后台必须设置  Access-Control-Allow-Origin 不能为 *

      Access-Control-Allow-Origin: "*" 和 Access-Control-Allow-Credentials", "true" 同时设置的时候Access-Control-Allow-Origin不能设置为"*"，只能设置为具体的域名。

16. 立即执行函数

    写法

    ```javascript
    (function foo(){}());
    (function foo(){})();
    ```

    主要原因是JavaScript 引擎规定，如果`function`关键字出现在行首，一律解释成语句。因此，JavaScript 引擎看到行首是`function`关键字之后，认为这一段都是函数的定义，不应该以圆括号结尾，所以就报错了。解决方法就是不要让`function`出现在行首，让引擎将其理解成一个表达式。最简单的处理，就是将其放在一个圆括号里面。

    推而广之，任何让解释器以表达式来处理函数定义的方法，都能产生同样的效果，比如下面三种写法

    ```javascript
    var i = function(){ return 10; }();
    true && function(){ /* code */ }();
    0, function(){ /* code */ }();
    ```

17. `EventLoop`的理解

    浏览器端Event Loop 包括一个函数执行栈，一个task队列(宏任务)和一个微任务队列

    **宏任务**： 

    - setTimeout
    - setInterval
    - setImmediate(node独有)
    - requestAnimationFrame(浏览器独有)，用来代码优化
    - I/O
    - UI rendering(浏览器独有)

    **微任务**： 

    - process.nextTick(Node独有)
    - Promise.then()异步执行， promise构造函数中的代码是同步执行的
    - Object.observe
    - MutationObserver

    每次循环只执行一个宏任务（macrotask），两个宏任务之间执行有时间差，每次执行宏任务的时候会把所有微任务执行完

18. 网页加载生命周期

    * `DOMContentLoaded`

      事件在DOM树构建完毕后被触发，我们可以在这个阶段使用js去访问元素；此时`async`和`defer`的脚本可能还没有执行；图片及其他资源文件可能还在下载中。

    * `load`

      在页面所有资源被加载完毕后触发，通常我们不会用到这个事件，因为我们不需要等那么久。

    * `beforeunload`

      在用户即将离开页面时触发，它返回一个字符串，浏览器会向用户展示并询问这个字符串以确定是否离开。

    * `unload`

      在用户已经离开时触发，我们在这个阶段仅可以做一些没有延迟的操作，由于种种限制，很少被使用。

20. `typeof`

    * 总是返回一个字符串
    * 可以正确识别number, boolean, string, object, function, undefined, symbol等七中类型
    * 会将null和Array识别为object, `NaN`识别为number
    * 为什么可以识别function,却识别不了array？

21. `instanceof` 和 `constructor`

    `instanceof`运算符用来验证，一个对象是否为指定的构造函数的实例。如果参数是原始类型的值，`Object`方法将其转为对应的包装对象的实例

    ```javascript
    var obj = Object(1);
    obj instanceof Object // true
    obj instanceof Number // true
    ```

23. `this`

    调用函数的时候，宿主环境会默认给函数传递一个参数`this`, 这个参数和普通的参数一样，只不过是默认传递的，谁调用这个函数，那么参数this就指向谁。

    构造函数下，this与被创建的新对象绑定在一起；DOM事件中，this指向触发事件的元素。

    为什么this始终执行函数运行时的环境呢？是因为对象的属性也分两种， 普通属性如number, string等，和引用属性，如function, obj等，引用属性可以通过对象调用，也可以全局调用，this是用来区分目前该引用属性所处环境的变量。

    **使用地址联系this的指向：**

    比如有一个对象obj，obj中有个方法`foo`。JavaScript 引擎内部，`obj`和`obj.foo`储存在两个内存地址，称为地址一和地址二。`obj.foo()`这样调用时，是从地址一调用地址二，因此地址二的运行环境是地址一，`this`指向`obj`。如果直接调用`foo()`，就是直接取出地址二进行调用，这样的话，运行环境就是全局环境，因此`this`指向全局环境。

    **严格模式中，如果this指向顶层对象，就会报错**

    **避免数组中的this**

    ```javascript
    var o = {
      v: 'hello',
      p: [ 'a1', 'a2' ],
      f: function f() {
        this.p.forEach(function (item) {
          console.log(this.v + ' ' + item);
        });
      }
    }
    
    o.f()
    // undefined a1
    // undefined a2
    ```

    上面的代码中，`forEach`回调函数中的this，其实指的是window对象。关于这点我有疑问，匿名函数中的this都指向window，但如果写成下面这种方式，为什么就可以呢？

    ```javascript
    var o = {
      v: 'hello',
      p: [ 'a1', 'a2' ],
      f: function f() {
        let self = this;
        this.p.forEach(function (item) {
          console.log(self.v + ' ' + item);
        });
      }
    }
    o.f()
    // hello  a1
    // hello  a2
    ```

24. call, apply, bind

    见深入理解javascript系列

    * 都是用来改变函数的运行环境

    * 第一个参数是一个对象，函数将运行在此对象上，this指向此对象，如果为空，null，或者undefined则this指向全局对象

    * apply的参数是一个数组，call的参数要一个一个列出来

    * bind 方法用于将函数体内的this绑定到一个对象，然后返回一个新函数

      ```javascript
      let obj = {
        count: 0,
        add: function(){
          return ++this.count;
        }
      }
      let fun = obj.add.bind(obj);  //1
      ```

    * 不能改变箭头函数的this指向

    如果某个对象想使用不属于他的方法，可以使用他们

    ```javascript
    funtion log(){
    	// 伪数组arguments转为真数组
    	let args = Array.ptototype.slice.apply(argument);
    	// args作为参数，不管参数数组中有多少个值，都可以一个一个输出
    	console.log.apply(console, args);
    }
    ```

25. 所有的参数都是按值传递的该如何理解？

    见深入理解javascript系列

    传递对象的时候，传递的是对象在内存中的地址的值，因此也算值传递

    ```javascript
    function foo(params){
        params.name = 'jack';
        params = {};
        params.name = 'tom'
    }
    
    let person = {}
    foo(person);
    console.log(person.name);
    ```

    在上面的例子中，首先person的内存地址传递进来后，添加name属性，而后params重新指向另外一个新对象，给新对象添加属性。所以现在obj引用的是另外一个局部对象了。person的name值仍然是jack。所以这里的“按值传递”的，引用类型传递进来传递的是它的内存数据（值）。

27. 提升

    JavaScript中遇到可执行代码块（全局和函数）时，先进行生成上下文的过程，这个过程会把所有的变量先声明，且函数声明优先级高于变量声明，如果函数名称和变量名称冲突，则会忽略变量声明。类似下面这种

    ```javascript
    // 预编译中声明变量a, 但是没有定义（赋值）。
    console.log(a);  // undefined
    var a = 1;
    
    // 如果没有使用var声明，或者使用了let,const声明，会报错，因为预编译过程中没有声明这个变量
    console.log(a);  // a is not defined
    let a = 1;
    // a = 1;
    
    // 函数的声明提升中，也会定义
    fun();  // 1    声明提升的时候定义了整个函数体，因此可以输出1
    function fun(){
        console.log(1);
    }
    
    // 换个方法，使用变量定义函数，遵循变量的声明方法
    fun() // fun is not defined
    var fun = function(){
        console.log(1);
    }
    ```

28. 性能优化常用方法

    * 减少Http请求
      * 使用webpack等合并js, css
      * 使用图片精灵
    * 减小资源体积
      * css压缩
      * js混淆
      * 图片压缩
      * gzip压缩
    * 缓存
      * 强缓存和协商缓存等网络层面的优化
    * 代码层面的优化
      * css的文件放在头部，js文件放在尾部或者异步
      * 尽量避免內联样式
      * 使用事件代理
      * 使用requestAnimationFrame代替setInterval操作
      * 减少重绘和回流
      * 减少dom操作
    * 渲染层面的优化
      * 图片懒加载

29. 继承

    构造函数的继承的几种方式

    ```javascript
    function Animal() {
        this.species = "动物";
    }
    
    function Cat(name, color) {
        this.name = name;
        this.color = color;
    }
    // 如上，有个动物和猫，如何让猫继承动物中的属性？
    
    //1、使用call或者apply方法,将父对象的构造函数绑定在子对象上
    function Cat(name, color) {
        Animal.apply(this, arguments);
        this.name = name;
        this.color = color;
    }
    let c = new Cat('tom', 'black');
    log(c)
    
    // 2、使用prototype方式
    Cat.prototype = new Animal();
    Cat.prototype.constructor = Cat;
    let c = new Cat('tom', 'black');
    log(c.species);
    
    // 3、直接继承prototype
    // 先将父对象改写
    function Animal() {
        //this.species = "动物";
    }
    Animal.prototype.species = "动物";
    // 然后继承
    Cat.prototype = Animal.prototype;
    Cat.prototype.constructor = Cat;
    let c = new Cat('tom', 'black');
    log(c.species);
    // 与上一种相比，这样做的优点是效率比较高（不用执行和建立Animal的实例了），比较省内存。缺点是 Cat.prototype和Animal.prototype现在指向了同一个对象，那么任何对Cat.prototype的修改，都会反映到Animal.prototype。
    
    // 4、利用空对象
    
    // 5、拷贝继承，把父对象的所有属性和方法，拷贝进子对象
    function extend(Child, Parent) {
        var p = Parent.prototype;
        var c = Child.prototype;
        for (var i in p) {
            c[i] = p[i];
        }
        c.uber = p;
    }
    extend(Cat, Animal);
    let c = new Cat("大毛","黄色");
    log(c.species);
    
    ```

30. 深拷贝

    ```javascript
    function deepCopy(p, c) {
        var c = c || {};
        for (var i in p) {
            if (typeof p[i] === 'object') {
                c[i] = (p[i].constructor === Array) ? [] : {};
                deepCopy(p[i], c[i]);
            } else {
                c[i] = p[i];
            }
        }
        return c;
    }
    ```

31. new一个对象会发生什么

    `https://github.com/mqyqingfeng/Blog/issues/13`

32. 数组去重

    `https://github.com/mqyqingfeng/Blog/issues/27`

    主要的方法：

    1. 定义一个新数组，双层循环给新数组中添加元素
    2. 同上一个方法，内循环变为使用indexOf判断
    3. 同上一个方法，外循环使用filter
    4. 利用对象属性的唯一性
    5. 使用set

33. 事件委托

    ```javascript
    ndContainer.addEventListener('click', function (e) {
            const target = e.target;
            if (target.tagName === 'LI') {
                alert(target.innerHTML);
            }
    });
    ```

34. 前端安全

35. 回流和重绘

    回流就是有元素改变时浏览器重新计算整个dom文档的位置和样式，重绘就是重新渲染到浏览器上。主要解决思路是防止回流，在对一个需要添加多种操作的元素上，可以先将此元素设置为不可见，让其脱离文档流，然后再操作，最后设为可见即可。

31. 防抖和节流

    函数防抖是指在事件被触发 n 秒后再执行回调，如果在这 n 秒内事件又被触发，则重新计时。这可以使用在一些点击请求的事件上，避免因为用户的多次点击向后端发送多次请求。

    ```javascript
    function debounce(func, wait) {
        var timeout = null;
        return function () {
            if(timeout) {
             	clearTimeout(timeout);
                timeout = null;
            }
            timeout = setTimeout(()=>{
                func.apply(this, arguments);
            }, wait);
        }
    }
    function handle(){
        console.log('1');
    }
    window.addEventListener('scroll', debounce(handle, 1000));
    ```

    函数节流是指规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行，如果在同一个单位时间内某事件被触发多次，只有一次能生效。节流可以使用在 scroll 函数的事件监听上，通过事件节流来降低事件调用的频率。

    ```javascript
    function throttle(fn, delay) {
      var preTime = Date.now();
    
      return function() {
        var context = this,
          args = arguments,
          nowTime = Date.now();
    
        // 如果两次时间间隔超过了指定时间，则执行函数。
        if (nowTime - preTime >= delay) {
          preTime = Date.now();
          return fn.apply(context, args);
        }
      };
    }
    ```

37. defer和async

    `defer`与`async`的区别是：`defer`要等到整个页面在内存中正常渲染结束（DOM 结构完全生成，以及其他脚本执行完成），才会执行；`async`一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染。一句话，`defer`是“渲染完再执行”，`async`是“下载完就执行”。另外，如果有多个`defer`脚本，会按照它们在页面出现的顺序加载，而多个`async`脚本是不能保证加载顺序的。

38. websocket

    存在的问题：断网之后，火狐浏览器会在几秒之后触发error和close事件，谷歌不会触发，而是要等到send()方法调用，浏览器才会发现链接断开了，便会立刻或者一定短时间后（不同浏览器或者浏览器版本可能表现不同）触发onclose函数（没有error函数），因此，写在这两个事件中的重连方法并不会执行。

    解决方法; 添加一个心跳机制，每隔一段时间发送一次数据，如果没有返回，则会触发onerror和onclose。

39. 


#### `css`

1. `css`实现`border`渐变

   使用border-image属性，不仅可以指定图片，也可以使用渐变

   ```css
   .gradient{
       width: 100px;
       height: 100px;
       border:10px solid #ddd;
       border-image: radial-gradient(#ddd,#000) 30;
   }
   ```

2. `css`实现阴影

   * 文字阴影: 使用text-shadow属性实现

     ```html
     div{
         text-shadow: 0 0 3px brown;
     }
     
     <div>
     	这里是要实现阴影的文字
     </div>
     ```

   * 元素阴影：使用box-shadow属性实现

     ```html
     div{
         box-shadow: 0 0 3px brown;
     }
     
     <div>
     	这里是要实现阴影的元素
     </div>
     ```

3. `css`各种定位方式

   * relative: 相对元素本来的位置进行定位，不会脱离文档流，需要注意的是元素原来的位置会被保留。经常作为使用absolute的元素的父元素，也可以使用z-index进行分层。
   * absolute:以父辈元素中最近的定位元素做参考坐标（使用relative, absolute, fixed定位的元素），如果父辈元素全都没有采用定位，则以`html`作为参考对象，脱离文档流。
   * fixed:定位的参考坐标为可视窗口。
   * static:默认定位

4. `css`实现水平垂直居中

   * 水平居中：可以给元素设定width，然后根据margin: auto实现水平居中。

   * 水平垂直居中：
     1. 使用flex布局： 低版本浏览器可能不支持
     2. 父元素相对定位，子元素绝对定位，使用`left,top(50%),margin-left,margin-top`调试。
     3. 同2，但是用`transform:translate(-50%,-50%);`代替`margin-left,margin-top`；

   

5. `css`实现一个旋转的圆

   使用animation实现绕着自身中心转的圆

   ```html
   .circle{
       width: 100px;
       height: 100px;
       border-radius: 50%;
       background: linear-gradient(brown, black);
       animation: rotateCircle 2s;
       animation-iteration-count: infinite;
       animation-timing-function: linear;
   }
   @keyframes rotateCircle{
       100% {transform: rotate(360deg)}
   }
   <div class="circle"></div>
   ```

   如果想实现绕着一个中心点转的圆，可以给元素添加`transform-origin`属性，改变旋转的中心点即可

   

6. 伪类和伪元素

   伪元素：伪元素能实现的功能，可以通过添加一个真的元素来实现，举个例子

   ```html
   // 使用伪元素实现
   p::first-letter{ color: red }
   <p>i am hungry</p>
   
   // 不使用伪元素
   p .first-letter{ color: red }
   <p><span class="first-letter">i</span> am hungry</p>
   ```

   伪类：顾名思义就是假的类，伪类能实现的功能，可以通过添加一个真的类来实现，举个例子

   ```html
   // 使用伪类,是第一个p元素颜色变红
   div p:nth-of-type(1){ color: red }
   <div>
       <p>first element</p>
   	<p>second element</p>
   </div>
   
   // 不想使用伪类，可以通过添加类来实现同样的功能
   .readP{ color: red }
   <div>
       <p class='redP'>first element</p>
   	<p>second element</p>
   </div>
   ```

   两者的区别： 如上：伪类的效果可以通过添加一个实际的类来达到，而伪元素需要添加一个实际的元素。因为写法和效果类似，经常有人搞混他们，所以`css3`规定伪类用一个冒号表示，伪元素用两个冒号，但因为兼容性问题，大部分还是一个冒号的写法，我们在书写是要养成良好习惯，伪元素要用两个冒号。

   常见的伪类：`:active  :link  :hover  :focus`等

   常见的（好像是全部）伪元素： `::after  ::before  ::first-letter  ::first-line  ::selection  ::backdrop`
   
7. 获取屏幕的高度，宽度等

   window.innerHeight: 返回文档的高度，这里的window对象指文档，而不是js中的全局变量。

   window.outerHeight: 包含工具条与滚动条的高度。

   screen.height: 屏幕的总高度

   screen.availHeight: 不包含windos任务栏的高度（浏览器高度，包括标签，书签等）
   
8. css中的选择器和他们的优先级

   `https://www.cnblogs.com/wangmeijian/p/4207433.html`

   - **ID选择器的特殊性值，加0,1,0,0**。
   - **类选择器、属性选择器或伪类，加0,0,1,0**。
   - **元素和伪元素，加0,0,0,1**。
   - **通配选择器\*对特殊性没有贡献，即0,0,0,0**。

   

9. css中的级联选择器优先级

10. css高度塌陷的解决

    父元素没有指定高度， 子元素又是浮动的，这样的话父元素的高度会是0， 浮动分为两种，使用float和使用绝对定位。当使用float时，主要解决方法如下几种

    * 父元素添加高度，不推荐，因为不能自适应子元素高度
    * 父元素设置overflow: hidden，
    * 推荐使用父元素添加after伪元素，会添加在最后一个子元素后面，将伪元素设置为clear: both。效果等同于子元素最后添加一个空元素并设置clear: both。

    如果是子元素绝对定位引起的，目前没有找到答案，只能通过js获取子元素高度后设置。

11. vertical-align的用法

    

12. 

#### `vue`

1. `vue`基本原理

   

2. 生命周期

   ###### 初始化阶段

   `beforeCreated`: 元素和数据都未绑定，此时还不能操作data中的数据，$route对象是存在的。

   `created`: 元素未绑定，数据已绑定，这里的数据包括data,  computed,  watch,  methods等。但是不能访问el, ref为空，在此阶段的DOM操作一定要放在`Vue.nextTick()`函数中。

   判断是否有el和template,有template将数据绑定到template上，没有的话绑定到`outHtml`上

   ###### 模板编译阶段

   在`created`后，`beforemount`前，有一个编译阶段，在此阶段，会先检查是否有`el`属性，如果没有，则停止生命周期直到`vm.$mount()`方法调用。如果有，再检查是否含有render函数，有的话将该函数中返回的内容当做模板，如果没有render函数，将template属性中的内容当做模板，如果没有template，将vm.$mount挂载的html当做模板，所以优先级是render > template > outHtml。如下，会将render中的App页面当成模板。

   ```javascript
   new Vue({
     store,
     router,
     render: h => h(App)
   }).$mount('#app');
   ```

   ###### 挂载阶段

   `beforeMounted`: 在此阶段，el属性已经存在，只不过是虚拟DOM，数据还没有挂载在模板中，此时，页面中的内容还是VUE中的占位符，data中的信息没有被挂载到DOM节点中。在这里可以在渲染前最后一次更改数据的机会，不会触发其他的钩子函数，一般可以在这里做初始数据的获取.

   `mounted`:  可操作dom和data。

   `beforeUpdata`: 此时data中的数据已经改变，el中的数据还没有变化，虚拟dom树生成（不确定。。）。另外，只有view中的数据变化才会进入该阶段，只是单纯的data中的数据不会触发。数据更新之前 可访问现有的DOM,如手动移除添加的事件监听器；可以在此时修改data，不会触发新的渲染过程。

   `update`: 数据已经改变，dom也重新渲染完成，注意不要在此阶段操作数据，会造成死循环，这里应该只执行依赖dom的操作，然而在大多数情况下，你应该避免在此期间更改状态。如果要相应状态改变，通常最好使用计算属性或 watcher取而代之。

   ###### 销毁阶段

   `beforeDestroy`: 实例在销毁之前进入该阶段，此时实例属性都可使用

   `destory`: Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。  执行destroy方法后，对data的改变不会再触发周期函数，此时的vue实例已经解除了事件监听以及和dom的绑定，但是dom结构依然存在。

   ###### 父子组件嵌套时触发顺序

   父beforeCreate->父created->父beforeMount->子beforeCreate->子created->子beforeMount->子mounted->父mounted

   ###### 子组件更新过程

   父beforeUpdate->子beforeUpdate->子updated->父updated

   ###### 父组件更新过程

   父beforeUpdate->父updated

   ###### 销毁过程

   父beforeDestroy->子beforeDestroy->子destroyed->父destroyed

3. 父子通信的方式

   * props
   * emit
   * $parent
   * $root
   * vuex
   * eventBus
   * slot
   * $attrs和$listeners

4. `v-model`如何实现，语法糖是什么

   v-model等同于两个指令v-bond: value='text', v-on:input='text = $event.target.value'

   上面是作用于input， 如果作用于自定义组件, 变为v-bond: value='text', v-on:input='text = $event'

5. `vuex`的原理及理解

6. `vue eventbus`的原理

7. 对数组元素的监听

8. 特性

   数据的双向绑定

   自定义组件

   计算属性（缓存值）

9. 缺点: 只适合于单页面

10. 不能监听数组和对象的情况

   * 数组通过索引值赋值和修改数组length属性时，解决方式使用splice方法或者this.$set(arr, index, value);

   * 对象属性的新增，删除

     其实Object.defineproperty()方法可以检测到数组通过索引改变值的，只不过为了性能考虑，没有监听

11. 计算属性和侦听器的区别

    在data中定义和未定义

    计算属性有缓存

    计算属性依赖其他属性，侦听器侦听其他属性

    侦听器的用途：当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。如侦听Input的值后进行异步的操作

12. slot的使用

13. 动态组件的切换

    `<myComponent v-bind:is='currentComponent'></myComponent>`

14. vuex中action和mutation的区别

    mutation是用来改变vuex中state，他是执行同步的操作

    action不能直接用来修改状态，提交的是mutation，执行的一般是异步的操作

15. vuerouter中的history模式

   

   

#### DOM

1. onclick和addEventListener的区别

   onclick对同一事件只绑定一次，而后者可以多次绑定

   ```javascript
   const nd = document.getElementById("nd");
   nd.onclick = function(){alert(1)};
   nd.onclick = function(){alert(2)}; 
   // 会忽略1， 只输出2
   nd.addEventListener('click', function(){alert(1)});
   nd.addEventListener('click', function(){alert(2)});
   // 1,2都会输出
   ```

   addEventListener第三个参数可以指定事件发生在捕获或者冒泡时，默认值为false，意为在冒泡阶段执行，如果为true,则在捕获阶段执行

   addEventListener对任何DOM都是有效的，而onclick仅限于HTML

2. 事件代理

   ```javascript
   const container = document.getElementById("liList");
   const arr = [1,2,3];
   for(var i = 0; i< arr.length; i++) {
       let ndItem = document.createElement('li');
       ndItem.innerText = i + 1;
       // ndItem.addEventListener('click', function(){
       //     alert(this.innerText)
       // })
       container.appendChild(ndItem);
   }
   // 直接在父元素上绑定事件，第三个参数不设，或设为false,在冒泡阶段执行
   // 冒泡阶段执行，意为先执行子元素上的事件，然后执行父元素上事件，捕获阶段执行则相反
   // 我试了将第三个参数改为true, 代码依旧可以执行，不知哪里出了问题。。
   container.addEventListener('click', function(event){
       // 改为true依旧可以执行的原因，我想就是因为target吧，在捕获阶段就拿到了event.target属性。
       // 另外target表示触发事件的元素，currentTarget表示绑定事件的元素
       console.log(event.target.innerText);
   }, false)
   ```

3. 