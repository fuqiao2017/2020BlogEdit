---
title: Vue 学习笔记
date: 2020-07-01 09:52:20
tags:
---
**要求** : 
1. Vue\Vue CLI 官方文档 要求精读一遍，多查资料多扩展
2. 尽量以代码的形式解决不懂的地方

- - -

如果你想给一个Vue组件添加生命周期函数有3个办法：

在Vue选项中添加
在模板中通过@hooks:created这种形式
vm.$on('hooks:created', cb)或者vm.$once('hooks:created', cb)

我们可以把组件区分为两类：一类是偏视图表现的 (presentational)，一类则是偏逻辑的 (logical)。我们推荐在前者中使用模板，在后者中使用 JSX 或渲染函数。