---
title: React 学习 笔记
date: 2020-07-09 17:45:19
tags:
---

react 解决复用方式 : 
render props / 高阶组件 (需要重新组织组件结构)
hooks
context
memoized

### Hook
__简介__:
1. Hook 使你在无需修改组件结构的情况下复用状态逻辑
2. Hook 使你在不编写 class 的情况下可以使用 state 以及更多的 React 特性
3. 在新的代码中同时使用 Hook 和 class
4. Hook 使用了 js 的闭包机制

__概览__:
1. State Hook
  内置的 Hook: useState...
  useState: 更新 state 是直接替换而不是合并
  useState 可以传入一个函数，React 只会在首次渲染时调用这个函数
  创建自己的 Hook: 复用不同组件之间的状态逻辑
2. Effect Hook
  Effect 副作用、作用，告诉 React 函数组件需要在渲染后执行某些操作
  useEffect: 注册一个副作用函数，可以执行副作用操作或者取消副作用(取消订阅)
  effect 可选清除机制
  useEffect 副作用函数可以通过返回一个清除函数来指定如何清除副作用
  React 会在组件卸载的时候执行清除操作
  useEffect 可在组件中多次使用
  React 等待浏览器完成渲染之后再延迟调用 useEffect

__Hook 使用规则__:
1. 只能在函数最外层使用
2. 只能在 React 的函数组件中使用

__自定义 Hook__:
1. 如果函数的名字以 use 开头并调用其他 Hook，就称为自定义 Hook，useSomething
2. 通过自定义 Hook，可以将组件逻辑提取到可重用的函数中

__其他 Hook__:
1. useContext 订阅 React 的 Context
  const locale = useContext(LocaleContext)
  接收一个 context 对象（React.createContext 的返回值）并返回该 context 的当前值。当前的 context 值由上层组件中距离当前组件最近的 <MyContext.Provider> 的 value prop 决定
2. useReducer 通过 Reducer 来管理组件本地的复杂 state
  const [todos, dispatch] = useReducer(todosReducer)


### 高级指引
__网络无障碍辅助功能__ Accessibility a11y
1. Fragment: 
  用于组合代码，示例: <Fragment key={item.id}>
  当不需要在 fragment 中添加任何 prop 时，可以使用短语法 <>
2. label 标记: jsx 中，label 的 for 应被写作 htmlFor
  <label htmlFor="nameInput">

__代码分割__
1. 动态 import 语法
  ```javascript
  import('./math').then(math => {
    console.log(math.add(1, 2))
  })
  // 当 webpack 解析到该语法时，会自动进行代码分割
  ```
2. React.lazy 和 Suspense 组件
in the future we plan to let Suspense handle more scenarios such as data fetching
  ```javascript
  // 单独打包 othercomp 组件代码，在需要的时候再下载代码渲染
  const OtherComp = React.lazy(() => import('./otherComp'))
  // React.lazy 接受一个函数，这个函数需要动态调用 import()
  // 函数必须返回一个 Promise，此 promise 需要 resolve 一个 export default 的 react 组件
  function lazyLoadMyComp() {
    return (
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComp/>
      </Suspense>
    )
  }
  // 可以用一个 suspense 组件包裹多个懒加载组件
  ```
3. 路由的代码分割
```javascript
  import React, {lazy} from 'react'
  const Home = lazy(() => import('./sceens/home.js'))
```

__Context__
context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递 的方法
目的: 目的是为了共享对于当前组件树而言是"全局"的数据，比如 主题 theme
1. class 组件中
  static contextType = ThemeContext
  在任何生命周期中访问 this.context
2. MyContext.Consumer
  需要 ***函数作为子元素*** 这种做法
  ```javascript
  <MyContext.Consumer>
    {value => /* 基于 context 值渲染 */}
  </MyContext.Consumer>
  ```
  可以在函数组件中使用
3. MyContext.displayName
  const MyContext = React.createContext(/* some value */);
  MyContext.displayName = 'MyDisplayName';
  <MyContext.Provider> // "MyDisplayName.Provider" 在 DevTools 中
  <MyContext.Consumer> // "MyDisplayName.Consumer" 在 DevTools 中
4. 最好将 provider 的 value 值保存到 state 里去
  ```javascript
  function ListItem() {
    return (
      <div>
        <ItemHeader/>
        // 这里，如果 Provider 重新渲染，则 它的 value 会被 替换一个新的对象 {xxx: '123456'}，虽然表面值是一样，但已经是不同的对象，因此会触发 provider 下面所有的 consumer 组件重新渲染
        <MyContext.Provider value={{xxx: '123456'}}>
          <ItemBody/>
        </MyContext.Provider>
      </div>
    )
  }
  class App extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        value: {something: 'something'},
      };
    }

    render() {
      return (
        // 将 value 提升到 state 中
        <Provider value={this.state.value}>
          <Toolbar />
        </Provider>
      );
    }
  }
  ```

__错误边界 ErrorBoundaries__
捕获渲染时不可意料的错误
错误边界无法捕获的错误:
1. 事件回调
2. 异步代码
3. 服务端渲染
4. 它自身抛出的错误
错误边界可以放在 APP 顶层，也可以在应用某个部分
```javascript
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

__Refs and Dom__
1. ref 是一个属性，用来 保存 挂载的 DOM 实例或者 React 组件实例
2. 适用场景:
  a. 管理焦点
  b. 触发强制动画
  c. 集成第三方 DOM 库
3. a. ref={this.xxxRef} this.xxxRef = React.createRef(null)
   b. ref={el => this.xxxRef = el} this.xxxRef = null
使用 ref 的方式:
1. 回调形式的 refs
```javascript
class CustomInput extends React.Component {
  constructor(props) {
    super(props)
    this.inputText = null
  }
  componentDidMount() {
    // 直接调用 DOM 原生 api
    this.inputText.focus()
  }
  render() {
    return <input ref={el => this.inputText = el}/>
  }
}
```
可以将 回调ref 传递给函数式子组件 以获取子组件内部的 ref
```javascript
function SonTextarea(props) {
  return <textarea ref={props.sonref}/>
}
class FatherDom extends React.Component {
  constructor(props) {
    super(props)
    this.mySonTextarea = null
  }
  componentDidMount() {
    console.log(this.mySonTextarea)
  }
  render() {
    return (
      // SonTextarea 组件中的 textarea 的 ref 将会保存到 FatherDom 父组件中的 mySonTextarea 属性中
      <SonTextarea sonref={el => this.mySonTextarea = el}>
    )
  }
}
```
2. React.createRef()
  这种方式通过 ***current*** 属性访问 DOM 节点
```javascript
class MyInput extends React.Component {
  constructor(props) {
    super(props)
    this.inputText = React.createRef()
  }
  handleInputFocus = () => {
    this.inputText.current.focus()
  }
  render() {
    return (
      <div>
        {/* 给 DOM 元素添加 ref */}
        <input ref={this.inputText}/>
      </div>
    )
  }
}
class App extends React.Component {
  //...省略一些代码
  render() {
    // 给 自定义 class 组件添加 ref
    // 然后可以手动调用 MyInput 内部的 聚焦 方法
    // 注意：函数组件没有实例，不能给函数组件添加 ref 属性
    // 在函数组件内部可以使用通过钩子 使用 ref
    return <MyInput ref={this.myInput}/>
  }
}
```

__Refs 转发 (forwarding-refs)__  使用 React.forwardRef 透传 ref
对于一些 高可复用 "叶子" 组件来说，有时候需要操作 DOM，比如焦点、动画，所以需要 ref 保存其 DOM 引用 
refs 转发使用场景:
1. 高复用性 函数式组件，需要操作其 DOM
```javascript
function FancyBut(props) {
  return <button onClick={props.clickFunc}>{props.children}</button>
}
// 使用 React.forwardRef 来获取传递给 FancyButton 的 ref
const FancyButton = React.forwardRef((props, forwordedRef) => {
  return <button ref={forwordedRef}>{props.children}</button>
})
const fbRef = React.createRef() // 此时 fbRef 就可以获取到 button 的 ref
<FancyButton ref={fbRef}>趣味按钮 1</FancyButton>
```
2. 高阶组件 HOC，需要 往接收的 Component 透传 ref
  ref 和 key 一样被 React 特殊处理，不是 props，正常情况不会往下传递
  ***通过将 ref 赋给 一个属性名称 中，可以传递 这个 ref (forwardedRef={ref})***
  然后内部组件可以通过属性 forwardedRef 将自身 DOM 引用保存到 这个 ref 中
```javascript
class FunnyComp extends React.Component {
  handleSomeFunc = () => {
    console.log('do something...')
  }
  render() {
    return (
      <div>
        <h3>这是 FunnyComp 组件</h3>
      </div>
    )
  }
}
export logPropsFunny = logProps(FunnyComp)
// 目的是获取到 WrappedComponent 的 ref，这样可以使用其内部的方法
function logProps(WrappedComponent) {
  class LogProps extends React.Component {
    render() {
      let {forwardedRef, ...rest} = this.props
      return <WrappedComponent {...rest} ref={forwardedRef}/>
    }
  }
  // return LogProps
  // React.forwardRef 返回的应该是 一个被包装后的类
  return React.forwardRef((props, ref) => {
    // This function should return a React node
    return <LogProps {...props} forwardedRef={ref}/> // 将 ref 保存到 forwardedRef 属性中
  })
}
// 使用
const fRef = React.createRef()
<logPropsFunny ref={fRef} name={'haha'}/>
// 这个时候，这个 fRef 保存的是 被包裹的 FunnyComp 的 ref，而不是 logProps 的 ref
// 然后可以通过 fRef 调用 FunnyComp 内部的方法 
```

__Fragments__
常用场景:
1. 表格 table > tr > td 不能用 div
2. dl > dt dd

__高阶组件 HOC__  High order components
1. 高阶组件是参数为组件，返回值为新组件的函数
2. React 组合特性
3. 不改变被包装组件，HOC 是必须是纯函数，不能有副作用
4. 不要在 render 方法中使用 HOC
  因为每次执行 HOC 函数都返回一个全新的值，会导致整个组件被重新挂载
5. Refs 不会被传递
6. 务必复制 被包裹组件 的静态方法
容器组件模式
```javascript
function getHocDisplayName (WrappedComponent) {
  return WrappedComponent.displayName || WrappedComponent.name || 'Component'
}
function enhanceSomethingHoc(WrappedComponent) {
  class EnhanceSome extends React.Component {
    calcByProp = () => {
      console.log('do something...')
      return 0
    }
    render() {
      let {extraProp, ...passThrough} = this.props
      let injectedProp = this.state.xxxState || this.calcByProp(extraProp)
      // 透传 props
      return <WrappedComponent injectedProp={injectedProp}  {...passThrough}/>
    }
  }

  EnhanceSome.displayName = getHocDisplayName(WrappedComponent) 

  return EnhanceSome
}
```

__深入 JSX__
1. JSX 仅仅是 React.createElement(component, props, ...children) 的语法糖
```javascript
<MyQuButton color="yellow" borderWidth="2">
  点击
</MyQuButton>
// 会被编译为:
React.createElement(
  MyQuButton,
  {color: 'yellow', borderWidth: '2'},
  '点击'
)
```
2. 自定义组件必须大写字母开头
3. 属性展开
```javascript
// <YourComp {...props}>
const Button = (props) => {
  const {kind, ...others} = props
  const clazzName = kind === 'primary' ? 'primaryBtn' : 'secondaryBtn' 
  return <button className={clazzName} {...others}/>
}
```
4. 注意: 一些 falsy 值，比如 0，仍然会被 React 渲染
```javascript
{ // list 为空数组时，下面这行代码会 渲染一个 0
  // list.length && <MessageList data={list}/>
  list.length > 0 && <MessageList data={list}/>
}
```

__虚拟化长列表__
使用 虚拟滚动技术: react-window react-virtualized

__性能优化__ optimizing-performance
shoudComponentUpdate 返回 true --> 调用 render 对比 渲染结果 --> 渲染结果有变化，则更新 DOM
React.PureComponent 对新旧 state、props 进行 **浅比较**, 当 state、props 包含数组或对象之类的值时，PureComponent 就不太适用了，因为比如数组的元素变化了，但数组本身没有变化，这时候 PureComponent 不会去更新组件
```javascript
// 解决上面的问题 是赋值一个全新的数组或者对象
class ListOfWords extends React.PureComponent {
  render() {
    return (
      <div>{this.props.words.join('-')}</div>
    )
  }
}
class MyPc extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      words: ['hello']
    }
  }
  setWords = () => {
    this.setState(prevState => ({
      words: prevState.words.concat(['new world'])
    }))
  }
  render() {
    return (
      <>
        <button onClick={this.setWords}>点击加词</button>
        <ListOfWords words={this.state.words}/>
      </>
    )
  }
}
```