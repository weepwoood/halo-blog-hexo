---
title: React
tags:
  - React
categories:
  - Web
large: true
cover: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/react.40t6xqt91be0.png
date: 2021-07-01 18:33:22
---

React 是一个用于构建用户界面的 JavaScript 库。

1. 采用组件化模式、声明式编码，提高开发效率及组件复用率。
2. 在 React Native 中可以使用 React 语法进行移动端开发。
3. 使用虚拟 DOM 和优秀的 Diffing 算法，尽量减少与真实 DOM 的交互。

关于虚拟 DOM：

1. 本质是 Object 类型的对象（一般对象）
2. 虚拟 DOM 比较“轻”，真实DOM比较“重”，因为虚拟 DOM 是 React 内部在用，无需真实 DOM 上那么多的属性。
3. 虚拟 DOM 最终会被 React 转化为真实 DOM，呈现在页面上。

> A4-JSX创建虚拟DOM


## 使用 React

### 通过 script 标签引入

```html
  <!-- 引入 react 核心库 -->
  <script type="text/javascript" src="../js/react.development.js"></script>
  <!-- 引入 react-dom，用于支持 react 操作 DOM -->
  <script type="text/javascript" src="../js/react-dom.development.js"></script>
  <!-- 引入 babel，用于将 jsx 转为 js -->
  <script type="text/javascript" src="../js/babel.min.js"></script>
```

> 注意引入顺序


## 创建虚拟 DOM

### 使用原生 JS 语法创建（不推荐）

```html
<div id="test"></div>

<script type="text/javascript">
    // 1.创建虚拟 DOM
    const VDOM = React.createElement('h1', {
        id: 'title'
    }, React.createElement('span', {}, 'Hello,React'))
    // 2.渲染虚拟 DOM 到页面 
    ReactDOM.render(VDOM, document.getElementById('test'))
</script>
```

> A3-JS创建虚拟DOM.html

### 使用 JSX 创建

```html
<div id="test"></div>

<!-- 此处一定要写 babel -->
<script type="text/babel">
    // 1.创建虚拟 DOM
    const VDOM =(
        <h1 id="title">
            <span>Hello,React</span>
        </h1>
    )  /* 此处一定不要写引号，因为不是字符串 */
    // 2.渲染虚拟 DOM 到页面 
    ReactDOM.render(VDOM,document.getElementById('test'))
    
    const TDOM = document.getElementById('test')
    console.log("虚拟DOM",VDOM);
    console.log("虚拟DOM",typeof VDOM);
    console.log("真实DOM",TDOM);
    debugger
</script>
```

> A4-JSX创建虚拟DOM.html

## JSX 语法

JSX 语法规则：

1. 定义虚拟 DOM 时，不要写引号。
2. 标签中混入 JS 表达式时要用 `{}`。
3. 样式的类名指定不要用 `class`，要用 `className`。
4. 内联样式，要用 `style={{key:value}}` 的形式去写。
5. 只有一个根标签
6. 标签必须闭合
7. 标签首字母：若小写字母开头，则将该标签转为 html 中同名元素，若 html 中无该标签对应的同名元素，则报错。若大写字母开头，react 就去渲染对应的组件，若组件没有定义，则报错。

> B-JSX语法规则

## 定义组件

### 函数式组件

```html
<!-- 准备好一个“容器” -->
<div id="test"></div>

<script type="text/babel">
  // 1.创建函数式组件
  function MyComponent(){
    console.log(this); //此处的this是undefined，因为babel编译后开启了严格模式
    return <h2>我是用函数定义的组件(适用于【简单组件】的定义)</h2>
  }
  // 2.渲染组件到页面
  ReactDOM.render(<MyComponent/>,document.getElementById('test'))
  /* 
    执行了ReactDOM.render(<MyComponent/>.......之后，发生了什么？
      1.React解析组件标签，找到了MyComponent组件。
      2.发现组件是使用函数定义的，随后调用该函数，将返回的虚拟DOM转为真实DOM，随后呈现在页面中。
  */
</script>
```

> C1-定义组件函数式组件.html

### 类式组件

```html
<!-- 准备好一个“容器” -->
<div id="test"></div>

<script type="text/babel">
  // 1.创建类式组件
  class MyComponent extends React.Component {
    render(){
      //render是放在哪里的？—— MyComponent的原型对象上，供实例使用。
      //render中的this是谁？—— MyComponent的实例对象 <=> MyComponent组件实例对象。
      console.log('render中的this:',this);
      return <h2>我是用类定义的组件(适用于【复杂组件】的定义)</h2>
    }
  }
  // 2.渲染组件到页面
  ReactDOM.render(<MyComponent/>,document.getElementById('test'))
  /* 
    执行了ReactDOM.render(<MyComponent/>.......之后，发生了什么？
        1.React解析组件标签，找到了MyComponent组件。
        2.发现组件是使用类定义的，随后new出来该类的实例，并通过该实例调用到原型上的render方法。
        3.将render返回的虚拟DOM转为真实DOM，随后呈现在页面中。
  */
</script>
```

## 主键的三大属性

### state

```html
<!-- 准备好一个“容器” -->
<div id="test"></div>

<script type="text/babel">
  //1.创建组件
  class Weather extends React.Component{
    
    //构造器调用几次？ ———— 1次
    constructor(props){
      console.log('constructor');
      super(props)
      //初始化状态
      this.state = {isHot:false,wind:'微风'}
      // ⭐解决changeWeather中this指向问题
      this.changeWeather = this.changeWeather.bind(this)
    }

    //render调用几次？ ———— 1+n次 1是初始化的那次 n是状态更新的次数
    render(){
      console.log('render');
      //读取状态
      const {isHot,wind} = this.state
      return <h1 onClick={this.changeWeather}>今天天气很{isHot ? '炎热' : '凉爽'}，{wind}</h1>
    }

    //changeWeather调用几次？ ———— 点几次调几次
    changeWeather(){
      //changeWeather放在哪里？ ———— Weather的原型对象上，供实例使用
      //由于changeWeather是作为onClick的回调，所以不是通过实例调用的，是直接调用
      //类中的方法默认开启了局部的严格模式，所以changeWeather中的this为undefined
      
      console.log('changeWeather');
      //获取原来的isHot值
      const isHot = this.state.isHot
      // ⭐严重注意：状态必须通过setState进行更新，且更新是一种合并，不是替换。
      this.setState({isHot:!isHot})
      console.log(this);

      //严重注意：状态(state)不可直接更改，下面这行就是直接更改！！！
      //this.state.isHot = !isHot //这是错误的写法
    }
  }
  //2.渲染组件到页面
  ReactDOM.render(<Weather/>,document.getElementById('test'))
      
</script>
```

state 简写

```html
<!-- 准备好一个“容器” -->
<div id="test"></div>

<script type="text/babel">
  //1.创建组件
  class Weather extends React.Component{
    //初始化状态
    state = {isHot:false,wind:'微风'}

    render(){
      const {isHot,wind} = this.state
      return <h1 onClick={this.changeWeather}>今天天气很{isHot ? '炎热' : '凉爽'}，{wind}</h1>
    }

    // ⭐自定义方法————要用赋值语句的形式+箭头函数
    changeWeather = ()=>{
      const isHot = this.state.isHot
      this.setState({isHot:!isHot})
    }
  }
  //2.渲染组件到页面
  ReactDOM.render(<Weather/>,document.getElementById('test'))
      
</script>
```

### props

```html
<!-- 准备好一个“容器” -->
<div id="test1"></div>
<div id="test2"></div>
<div id="test3"></div>

<script type="text/babel">
  //创建组件
  class Person extends React.Component{
    render(){
      // console.log(this);
      const {name,age,sex} = this.props
      return (
        <ul>
          <li>姓名：{name}</li>
          <li>性别：{sex}</li>
          <li>年龄：{age+1}</li>
        </ul>
      )
    }
  }
  //渲染组件到页面
  ReactDOM.render(<Person name="jerry" age={19}  sex="男"/>,document.getElementById('test1'))
  ReactDOM.render(<Person name="tom" age={18} sex="女"/>,document.getElementById('test2'))

  const p = {name:'老刘',age:18,sex:'女'}
  // console.log('@',...p);
  // ReactDOM.render(<Person name={p.name} age={p.age} sex={p.sex}/>,document.getElementById('test3'))
  ReactDOM.render(<Person {...p}/>,document.getElementById('test3'))
</script>
```

#### 对 props 进行限制

> 注意需要引入 prop-types.js

```html
<!-- 准备好一个“容器” -->
<div id="test1"></div>
<div id="test2"></div>
<div id="test3"></div>

<!-- 引入prop-types，用于对组件标签属性进行限制 -->
<script type="text/javascript" src="../js/prop-types.js"></script>

<script type="text/babel">
  //创建组件
  class Person extends React.Component{
    render(){
      // console.log(this);
      const {name,age,sex} = this.props
      //props是只读的
      //this.props.name = 'jack' //此行代码会报错，因为props是只读的
      return (
        <ul>
          <li>姓名：{name}</li>
          <li>性别：{sex}</li>
          <li>年龄：{age+1}</li>
        </ul>
      )
    }
  }
  //对标签属性进行类型、必要性的限制
  Person.propTypes = {
    name:PropTypes.string.isRequired, //限制name必传，且为字符串
    sex:PropTypes.string,//限制sex为字符串
    age:PropTypes.number,//限制age为数值
    speak:PropTypes.func,//限制speak为函数
  }
  //指定默认标签属性值
  Person.defaultProps = {
    sex:'男',//sex默认值为男
    age:18 //age默认值为18
  }
  //渲染组件到页面
  ReactDOM.render(<Person name={100} speak={speak}/>,document.getElementById('test1'))
  ReactDOM.render(<Person name="tom" age={18} sex="女"/>,document.getElementById('test2'))

  const p = {name:'老刘',age:18,sex:'女'}
  // console.log('@',...p);
  // ReactDOM.render(<Person name={p.name} age={p.age} sex={p.sex}/>,document.getElementById('test3'))
  ReactDOM.render(<Person {...p}/>,document.getElementById('test3'))

  function speak(){
    console.log('我说话了');
  }
</script>
```

#### props 的简写方式

```html
<!-- 准备好一个“容器” -->
<div id="test1"></div>
<div id="test2"></div>
<div id="test3"></div>

<!-- 引入prop-types，用于对组件标签属性进行限制 -->
<script type="text/javascript" src="../js/prop-types.js"></script>

<script type="text/babel">
  //创建组件
  class Person extends React.Component{

    constructor(props){
      //构造器是否接收props，是否传递给super，取决于：是否希望在构造器中通过this访问props
      // console.log(props);
      super(props)
      console.log('constructor',this.props);
    }

    //对标签属性进行类型、必要性的限制
    static propTypes = {
      name:PropTypes.string.isRequired, //限制name必传，且为字符串
      sex:PropTypes.string,//限制sex为字符串
      age:PropTypes.number,//限制age为数值
    }

    //指定默认标签属性值
    static defaultProps = {
      sex:'男',//sex默认值为男
      age:18 //age默认值为18
    }
    
    render(){
      // console.log(this);
      const {name,age,sex} = this.props
      //props是只读的
      //this.props.name = 'jack' //此行代码会报错，因为props是只读的
      return (
        <ul>
          <li>姓名：{name}</li>
          <li>性别：{sex}</li>
          <li>年龄：{age+1}</li>
        </ul>
      )
    }
  }

  //渲染组件到页面
  ReactDOM.render(<Person name="jerry"/>,document.getElementById('test1'))
</script>
```