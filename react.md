## react设计思想

在web开发的早期，页面都是从服务器端返回的，每次数据发生变化时，会重新返回一次。这样即使是部分数据变化也要重新渲染整个页面，有点太麻烦了，因此开发者们想出通过操作dom来改变页面的方法，这就是最简单的mvc模式，当数据（model）发生变化时通过control操作dom，从而实现view的改变，这样做任然需要写不少的代码，直到M-V-VM模式的出现，在这个模式中，model和view不变，control变成了viewmodel，比如整个angular框架就是一个viewmodel，model发生变化时，view通过vm自动更新，而不需要开发者再写什么代码。

react摒弃了这种模式，它不仅不是mvvm， 甚至不是mvc。他回归了最原始的思路，就是每次数据发生变化，就重新执行一次整体渲染。不过这次渲染不是实际的dom，而是虚拟dom，再通过diff算法，对比上一次渲染得到的虚拟dom，然后将变化的地方更新到真实 DOM 上。

这样我之前的疑问就解答了，react中的全部渲染是指虚拟dom的全部渲染，部分渲染是指真是dom的渲染。

参考：`https://www.jianshu.com/p/bef1c1ee5a0e`

**数据绑定和组件化**

`es6`中的`import`文件时会先执行文件吗？（yes）

### react的核心概念

* 元素渲染

* 组件和props

* state和生命周期
  正确使用state，不要直接修改state的值，而是使用`setState`方法，构造函数是唯一可以给state赋值的地方。

  **生命周期**

  ##### 创建阶段

  * `componentWillMount`: 相当于`vue`中的created
  * `render`： 第一次开始渲染真正的虚拟 DOM，当 render 执行完，内存中就有完整的虚拟 DOM 了
  * `componentDidMount`： 相当于`vue`中的`mounted` 

  ##### 运行阶段

  * `componentWillReceiveProps`： 组件将要接收新属性，此时，只要这个方法被触发，就证明父组件为当前子组件传递了新的属性值
  * `shouldComponentUpdate`： 组件是否需要被更新，此时，组件尚未被更新，但是，state 和 props 肯定是最新的
  * `componentWillUpdate`： 组件将要被更新，此时，尚未开始更新，内存中的虚拟 DOM 树还是旧的，页面上的 DOM 树也是旧的
  * `componentDidUpdate`： 此时页面又被重新渲染了， `state`和`虚拟 DOM`和`页面`已经完成保持同步
  * `render`： 此时，又要重新根据最新的 state 和 props 重新渲染一颗内存中的虚拟 DOM 树，当 render 调用完毕，内存中的旧 DOM 树，已经被新 DOM 树替换了，此时页面还是旧的

  ##### 销毁阶段

  * `componentWillUnmount`：组件将要被卸载，此时组件还可以正常使用

* 事件处理

* 条件渲染

* 循环和key值

* 表单

* 状态提升

* 组合和继承

* 虚拟DOM和diff算法

  

  


### 高级指引

**代码分割**

使用import()语法；使用`React.lazy`和Suspense

**context**

context设计目的是为了共享那些对于一个组件树而言是全局的数据，例如当前认证的用户，主题，首选语言等在父组件和子组件中都可能会用到的数据。

**错误边界**

**Refs转发**

**Fragments**

用法和`vue`中的template类似，如子组件中是`td`元素，如果用`div`包裹，返回给父组件使用不了，这时可以用Fragments代替

```react
// 唯一可以接受的属性是key
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```

**高阶组件**

参数为组件，返回值为组件的函数

导入的`css`会在全局中生效，因为样式表没有模块作用域，解决办法

1. 加global

   ```scss
   /*使用这种方式写的不会被模块化*/
   :global(.test){
       font-style: italic;
   }
   ```

2. 在`webpack`中进行配置



### react小书

1. 受控组件和非受控组件

   组件是否受控，是针对表单而言的

   受控组件，表单元素的修改会实时映射到状态值上，类似于双向绑定。受控组件只有继承React.Component才会有状态，加上onChange事件绑定对应的值；

   非受控组件即不受状态的控制，获取数据就是相当于操作DOM。有两种方法可以获取到值

   第一种是函数，在虚拟DOM节点上使用ref，并使用函数，将函数的参数挂载到实例的属性上。

   ```react
   handleSubmit = (e) => {
       // 阻止原生默认事件的触发
       e.preventDefault();
       console.log(this.username.value);
   }
   render() {
       return (
           <form onSubmit={this.handleSubmit}>
               用户名<input
                   name="username"
                   type="text"
                   ref={username=>this.username=username}
               /><br />
           </form>
       )
   }
   ```

   第二种方式：通过构造函数声明的方式

   新语法，实例的构造函数constructor这创建一个引用，在虚拟DOM节点上声明一个ref属性，并且将创建好的引用赋值给这个ref属性，react会自动将输入框中输入的值放在实例的second属性上。

   ```react
   constructor(){
       super();
       // 在构造函数中创建一个引用
       this.second=React.createRef();
   }
   handleSubmit = (e) => {
       // 阻止原生默认事件的触发
       e.preventDefault();
       console.log(this.second.current.value);
   }
   render() {
       return (
           <form onSubmit={this.handleSubmit}>
               {/* 自动将输入框中输入的值放在实例的second属性上 */}
               密码<input
                   name="password"
                   type="text"
                   ref={this.second}
               /><br />
           </form>
       )
   }
   ```

   

2. props.children

3. defaultProps

4. 状态提升（兄弟组件传递数据）

5. 生命周期。（constructor, componentWillMount, render, 渲染，componentDidMount , 卸载阶段清除定时器等）。更新的生命周期。

6. dangerouslySetHTML: 防止转义html内容

7. 使用propsType给参数做类型限制

8. 组件的命名和方法的摆放

9. 高阶组件 (纯函数)

10. content

11. redux



### Q&A

1. react中父子组件的生命周期顺序？

   先执行父组件的componentWillMount，后执行子组件的；先执行子组件的componentDidMount,后执行父组件的。

2. 为什么ajax方法要放在componentDidMount中，而不是挂载之前？

   没搞懂

3. getInitialState 平安门户角色编辑中的方法？？

4. 上上级组件直接传值；



loadsh, underscore
