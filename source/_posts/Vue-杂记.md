---
title: Vue 杂记
date: 2019-09-30 19:44:40
categories: Skills
index_img:
banner_img:
tags: Vue
updated: 2019-09-30 19:44:40
---

## Bug：改变 type 时 popper 定位错误

> 记一次 `el-date-picker` debug 经过

**Bug 复现链接**

https://codepen.io/Hitoka/pen/oNgLjQX


**解决方法**

1. 从定位入手，找到影响 popper 定位的函数

在源码中找到了一个函数 `this._getOffsets(this._popper, this._reference, data.placement);` ，猜想应该和 popper 的定位有关，它接收三个参数。要搞明白三个参数分别指向谁，只需要在每次调用这个函数前打印一下三个参数的值便可。`this._popper` 指向 popper，`this._reference` 指向选择框。

按正常逻辑，`this._reference` 应该随着选择框类型的改变而改变，然而在几次打印后，发现 `this._reference` 并没有随选择框而改变。我想，问题应该就出在这里。

2. `this._reference` 为什么没有变？
  

`this._reference` 的值来自一个形参 `reference`，它是随 `Popper` 的构造函数 `function Popper(reference, popper, options)` 一起传入的，接着全局搜索 `new Poppe`r，成功定位到 `this.popperJS = new PopperJS(reference, popper, options)`，这里的 `reference` 是一个计算属性，如下

```js
computed() {
  reference() {
        const reference = this.$refs.reference;
        return reference.$el || reference;
  }
}
```
这里的 `this.$refs.reference` 指向当前选择框，问题仿佛就出在这里，我们都知道 `$refs` 不是响应式的，所以 `reference` 并不会随 `this.$refs.reference` 而变化。

3. 解决问题

我的解决方法如下

```js
updated() {
    // HACK: 解决 date-picker 随 type 变化时定位错误问题
    if (['create_time', 'last_business', 'birthday', 'daterange'].includes(this.type)) {
      this.$refs.datePicker.referenceElm = this.$refs.datePicker.$el
    }
  },
```

### 如何判断一个元素失去焦点

一般判断一个元素失去焦点需要判断以下几点：

- 点击的目标不是该元素
- 点击的目标不是该元素的子元素
- （如果元素带下拉框）点击的目标不是下拉框

解决这个问题需要注意两点，一是参与的事件有 `mouseup` 和 `mousedown`，二是可以使用 `contains` 来判断元素是否包括某元素。`Element` 中 `v-clickoutside` 指令便实现了这个功能，其中判断元素失焦的条件有：

```js
  if (!vnode ||
      !vnode.context ||
      !mouseup.target || 
      !mousedown.target ||
      el.contains(mouseup.target) || // 点击的目标是该元素子元素
      el.contains(mousedown.target) ||
      el === mouseup.target ||
      (vnode.context.popperElm &&
      (vnode.context.popperElm.contains(mouseup.target) || // 点击的目标是下拉框的子元素
      vnode.context.popperElm.contains(mousedown.target))))
```

## 关于 `el-form` 的 `restFields` 方法

编辑某表单时，需要先给表单赋初值，再进行修改、保存，然后关闭表单同时使用 `restFields` 重置表单（希望的是清空的效果）。接着再点击新建表单时，问题出现了，表单没有如希望的那样为空，而是保留了上次编辑时赋的初值。

经过查阅资料，发现是因为 `restFields` 方法会重置到表单 `mounted` 之前的值，也就是说，当我第一次点击编辑后，随即进行了赋初值，这个动作发生在表单的 `mounted` 执行之前，且是 `mounted` 之前的最后一次赋值，所以它被重置到了这次的值。然而我希望的是重置为空值，网上的解决方法是把给表单赋值的动作放在 `nextTick` 的回调里，`nextTick` 会等待 DOM 渲染完毕后才执行回调函数，果然这样就达到了目的，但是，这个解决方法引发了我的两个问题：

   1. `nextTick` 的原理是什么？
   2. Vue 的生命周期与 `nextTikc` 是什么关系？（主要是 `mounted` 和 `nextTick` 有什么关系）

（本周只弄懂了第一个问题，第二个问题待下周解决。）

在阅读了一些文章后，我个人觉得可以把 `nextTick` 简单理解为 `setTimeout`（Vue 源码中实现 `nextTick` 用了三种可选方法，按优先级分别是 `Promise`、`MutationObserver`、`setTimeout`），`nextTick` 里的回调函数会被放在**任务队列**中，等待主线程执行栈里为空后才会被放到执行栈中执行（这里涉及到 Event Loop，可以参考 [《JavaScript 运行机制详解：再谈 Event Loop》](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)）。Vue 源码中一个典型的使用 `nextTick` 的地方是 DOM 的异步更新，具体流程参考[《从Vue.js 源码看异步更新 DOM 策略及 nextTick》](https://juejin.im/post/59c7b25a5188257a125d7a98) ，该流程可以总结为 `data 改变 -> Dep -> watcher -> update -> queueWatcher -> nextTick(flushSchedulerQueue)`。


## scoped css 如何作用在子组件上？

为了不让 css 污染其他组件，我们往往会加上 `scoped`，但这也阻止了在父组件里更改子组件的样式。解决方法是在 css 选择器前加上 `>>>`，在 vue 项目中也可以使用 `/deep/` 替代，但是会出现 vue 不编译的问题，导致不兼容某些浏览器， 所以还是用 `>>>` 吧。