### chrome调试技巧

#### copy & save

copy(localstorage); 复制全局变量

保存堆栈信息

点击... copy html

#### 快捷键

ctrl + [ 切换面板

ctrl + shift + d切换devtool的位置

#### command

切换主题，在command中输入theme

先选中节点，再输入screen将节点保存为图片

#### Snippets的使用

将代码放进snippets中，然后通过右键或者快捷键ctrl+ enter即可运行

#### console中的$

如果在应用中没有定义$, 那么$等价于document.querySelector， $$不仅执行 `document.QuerySelectorAll` 并且它返回的是：一个节点的 **数组** ，而不是一个 `Node list`

$_输出上一次执行的结果

$i下载npm包，如$i('loadsh');

#### 条件断点

循环中只对某次循环感兴趣，此时可以edit breakpoint，添加一个条件如i===10， 会在此时触发断点，其余不触发。

可以使用条件断点将console.log连接到代码中，不需要在源代码中使用console.log， console.table等

#### 自定义输出格式

#### 打印时使用增强对象字面量

当有多个变量时使用，可以输出一个对象

```javascript
let name = 'xiaoming';
console.log({name});
```

#### 使用console.table

如果变量是一个数组或对象，可以使用console.table打印出一个漂亮的表格，第二个参数可以输入想要展示的列名

```javascript
let arr = [{name: 'xiao', age: 10},{name: 'xiao', age: 10}, {name: 'xiao', age: 10}];
console.table(arr, 'name');
```

可以和上面的增强对象字面量合用

```javascript
console.table({name, age, address});
```

#### 使用console.dir打印html节点

这样打印出的节点带有全部的属性。

#### network的使用

自定义请求表，可以添加cookie， method等。

重新发送xhr请求

添加Url的一部分作为断点，会在触发时进入debug模式

#### 元素面板操作

可以通过拖动的方式移动dom的位置，也可以使用ctrl + 方向键实现