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