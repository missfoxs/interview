1. 实现深拷贝

   ```javascript
   let obj = {
       a: 1,
       b: {
           x: 2
       },
       c: function () {},
       d: [1, 2, 3]
   };
   let arr = [1, 2, {
       a: 1,
       b: [1, 2, 3]
   }]
   // let copyObj = obj; // 浅拷贝，改变copyObj的值，obj也会变
   
   // 深拷贝方法之一，使用JSON
   //let copyObj = JSON.parse(JSON.stringify(obj));
   // 深拷贝方法之二，递归调用, 数组或者对象都可以使用该方法
   function deepClone(obj) {
       let copy = obj instanceof Array ? [] : {};
       for (let i in obj) {
           if (obj.hasOwnProperty(i)) {
               copy[i] = typeof obj[i] === 'object' ? arguments.callee(obj[i]) : obj[i];
           }
       }
       return copy
   }
   let copyObj = deepClone(obj);
   copyObj.a = 3;
   copyObj.d = [];
   //log(obj, copyObj);
   let copyArr = deepClone(arr);
   copyArr[2].b = {};
   // log(arr, copyArr);
   ```

2. 使用`setTimeout`模拟`setInterval`

   ```javascript
   function demo() {
       log(1);
   }
   setInterval(demo, 1000)
   setTimeout(function(){
       demo()
       setTimeout(arguments.callee, 1000)  //arguments.callee 指向当前函数自身
   }, 1000)
   ```

3. 实现一个双向数据绑定，使用`Object.defineProperty`方法，当对象的的属性更新时，修改对应的元素的`InnerHTML`的值

   ```javascript
   let input = document.getElementById('input')
   let span = document.getElementById('span')
   let obj = {
       name: 'xiaoming'
   };
   Object.defineProperty(obj, 'name', {
       set: function (newVal) {
           input.value = newVal;
           span.innerHTML = newVal;
       }
   })
   input.addEventListener('keyup', function () {
       obj.name = input.value;
   })
   ```

4. `instanceof`的实现原理

   ```javascript
   // 右边的对象要在左边对象的原型链上
   // 实现instanceof
   function myInstanceof(left, right) {
       while (true) {
           if (left.__proto__ === null) {
               return false;
           }
           if (left.__proto__ === right.prototype) {
               return true;
           }
           left = left.__proto__;
       }
   }
   
   function Person(name) {
       this.name = name;
   };
   
   function Teacher(age) {
       this.age = age;
   }
   
   Teacher.prototype = new Person();
   let tom = new Teacher('tom', 18);
   //log(myInstanceof(tom, Person))
   ```

5. `Object.create`的实现原理

   ```javascript
   myCreate = function (obj) {
       // let res = {};
       // res.__proto__ = obj;
       // return res;
       // 殊途同归的方法
       function F() {};
       F.prototype = obj;
       return new F();
   }
   let person = {
       age: 18,
       say() {
           log(`my name is ${this.name}, and i am ${this.age} years old`);
       }
   }
   let me = myCreate(person);
   me.name = 'xiaoming';
   // me.say();     //my name is xiaoming, and i am 18 years old
   ```
   

